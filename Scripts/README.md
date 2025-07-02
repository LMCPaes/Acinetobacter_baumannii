R

# Load necessary libraries
# Ensure these packages are installed in your R environment:
# install.packages(c("ggplot2", "dplyr", "ggrepel", "pheatmap"))

library(ggplot2)
library(dplyr)
library(ggrepel)
library(pheatmap) # Make sure pheatmap is loaded as it's used for the heatmap

# 1. Define the path to the folder where your .tsv files are located
# Make sure to use forward slashes (/) or double backslashes (\\) for Windows paths.
input_files_path <- "C:/Users/Milla/Downloads/" # Adjust this path as needed

# 2. Load each AMRFinderPlus result file and add a 'Strain' column
# This allows us to know which strain each gene was identified from.

# Function to load and add strain name
load_amr_data <- function(file_name, strain_name, path = input_files_path) {
  read.delim(paste0(path, file_name), header = TRUE, sep = "\t") %>%
    mutate(Strain = strain_name)
}

data_aye <- load_amr_data("resultados_amrfinder_AYE.tsv", "AYE")
data_atcc19606 <- load_amr_data("resultados_amrfinder_ATCC19606.tsv", "ATCC19606")
data_acicu <- load_amr_data("resultados_amrfinder_ACICU.tsv", "ACICU")
data_ab5075 <- load_amr_data("resultados_amrfinder_AB5075.tsv", "AB5075")

# 3. Combine all strain dataframes into a single dataframe
# The bind_rows function from dplyr is ideal for combining dataframes with the same columns.
all_amr_genes <- bind_rows(data_aye, data_atcc19606, data_acicu, data_ab5075)

# Optional: View the first rows and structure of the combined dataframe (for your verification)
# print("First rows of the combined dataframe:")
# print(head(all_amr_genes))
# print("Structure of the combined dataframe:")
# print(str(all_amr_genes))
# print("Count of genes per strain in the combined dataframe:")
# print(table(all_amr_genes$Strain))

# 4. Define the molecular resistance targets monitored by the WHO GLASS system for Acinetobacter spp.
# We will use regular expressions to ensure variants (e.g., OXA-23, mcr-1) are captured.
glass_targets_carbapenem <- c("NDM", "OXA", "VIM", "IMP", "GES", "KPC")
glass_targets_colistin <- c("mcr") # 'mcr' will capture all variants like mcr-1, mcr-2, etc.

# 5. Add new columns to the dataframe to indicate if a gene is a GLASS target
# We use grepl() to check for the presence of GLASS target patterns in 'Element.symbol' or 'Element.name' fields.
# Create a copy to avoid modifying the original dataframe directly.
all_amr_genes_glass <- all_amr_genes

all_amr_genes_glass <- all_amr_genes_glass %>%
  mutate(
    Is_GLASS_Carbapenem = grepl(paste(glass_targets_carbapenem, collapse = "|"), Element.symbol, ignore.case = TRUE) |
      grepl(paste(glass_targets_carbapenem, collapse = "|"), Element.name, ignore.case = TRUE),
    Is_GLASS_Colistin = grepl(paste(glass_targets_colistin, collapse = "|"), Element.symbol, ignore.case = TRUE) |
      grepl(paste(glass_targets_colistin, collapse = "|"), Element.name, ignore.case = TRUE),
    # A gene is considered a 'GLASS Target' if it's either a carbapenem or colistin target.
    Is_GLASS_Target = Is_GLASS_Carbapenem | Is_GLASS_Colistin
  )

# 6. Summarize and Count the GLASS Target Resistance Genes found by Strain
# Filter only GLASS target genes, group by strain, element symbol, class, and subclass,
# then count the occurrence of each.
summary_glass_by_strain <- all_amr_genes_glass %>%
  filter(Is_GLASS_Target == TRUE) %>%
  group_by(Strain, Element.symbol, Class, Subclass) %>%
  summarise(Count = n(), .groups = 'drop') %>%
  arrange(Strain, desc(Count)) # Order for easier visualization

# Count the number of *unique* GLASS genes found in each strain
count_unique_glass_by_strain <- all_amr_genes_glass %>%
  filter(Is_GLASS_Target == TRUE) %>%
  group_by(Strain) %>%
  summarise(Unique_GLASS_Genes = n_distinct(Element.symbol), .groups = 'drop')

# 7. Print results for your review
print("--- ANALYSIS RESULTS ---")

print("First rows of the dataframe with GLASS Target identification:")
print(head(all_amr_genes_glass[, c("Strain", "Element.symbol", "Class", "Subclass", "Is_GLASS_Carbapenem", "Is_GLASS_Colistin", "Is_GLASS_Target")]))

print("\nResistance Genes (GLASS Targets) found by Strain and their count:")
print(summary_glass_by_strain)

print("\nUnique GLASS Genes count per Strain:")
print(count_unique_glass_by_strain)

# --- Plot 1: Bar Chart of Unique GLASS Resistance Genes per Strain ---
ggplot(count_unique_glass_by_strain, aes(x = reorder(Strain, -Unique_GLASS_Genes), y = Unique_GLASS_Genes, fill = Strain)) +
  geom_bar(stat = "identity", color = "black") +
  # geom_text line removed to not display numbers on top of bars
  labs(
    #title = "Count of Unique GLASS Target Resistance Genes per Strain", # Uncomment to add title
    x = "Strain",
    y = "Number of Unique GLASS Genes"
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(hjust = 0.5, face = "bold", size = 14, margin = margin(t = 10, b = 10)),
    axis.title = element_text(size = 12, face = "bold"),
    axis.text.x = element_text(angle = 45, hjust = 1),
    legend.position = "none",
    plot.margin = unit(c(1, 1, 1, 1), "cm")
  )

# --- Plot 2: Heatmap of Presence/Absence of Specific GLASS Genes by Strain ---

# Prepare data for the heatmap
glass_genes_present <- all_amr_genes_glass %>%
  filter(Is_GLASS_Target == TRUE) %>%
  select(Strain, Element.symbol) %>%
  distinct()

heatmap_matrix <- table(glass_genes_present$Element.symbol, glass_genes_present$Strain)

heatmap_df <- as.data.frame.matrix(heatmap_matrix)
heatmap_df_numeric <- as.matrix(heatmap_df)
heatmap_df_numeric[heatmap_df_numeric >= 1] <- 1

# Define colors for the heatmap
heatmap_colors <- c("white", "darkgreen") # White for absent, Dark green for present

# Create the Heatmap with legend adjustments
pheatmap(
  heatmap_df_numeric,
  color = heatmap_colors,
  cluster_rows = FALSE,
  cluster_cols = FALSE,
  #main = "Presence/Absence of GLASS Target Resistance Genes per Strain", # Uncomment to add title
  fontsize_row = 10,
  fontsize_col = 10,
  display_numbers = TRUE, # Display 0s and 1s in the cell
  # Legend adjustments:
  # Explicitly define breaks and labels for the legend
  legend_breaks = c(0, 1),
  legend_labels = c("Absent", "Present"),
  legend = TRUE, # Ensure legend is displayed
  # Adjust general font size to better accommodate elements, including title and legend
  fontsize = 12
)
