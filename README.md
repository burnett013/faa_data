# FAA Aircraft Registration Dataset Preparation Log
This document provides a comprehensive record of manny of the actions taken to clean, transform, and organize FAA aircraft registration data for analytical purposes. It consolidates work performed on both the full FAA dataset (faa_reg_data_v1.xlsx) and the kits dataset (kits.xlsx), which contains only kit-built aircraft records in the US. The goal is to build robust datasets suitable for on-going analysese into both the overall general aviation (GA) industry and the kit-built aircraft segment.

*Note: All materials are available to the reader in this repo as desired, however I did hardcode the file paths for simplicity so that aspect will need to be adjust.*

## Project Overview
The FAA Aircraft Registration dataset contains over 300,000 records detailing U.S. civil aircraft registrations. Due to its size and complexity, the dataset was split into two parts:
• `faa_reg_data_v1.xlsx` → The full master dataset.
• `kits.xlsx` → A subset (~16,000 rows) containing only kit-built aircraft registered in the U.S.
The original data was downloaded from the [FAA website](https://www.faa.gov/licenses_certificates/aircraft_certification/aircraft_registry/releasable_aircraft_download), as was the data dictionary that accompanied it. 

*Note: Both the original dictionary and a cleaned, modified version of the dictionary is included in this repo.*

Though it was originally downloaded as a zip file with 7 individual sheet, for ease of use I chose to merge relevant sheets, columns, and/or data that would help identify trends in the general aviation industry. This resulted in `faa_reg_data_v1.xlsx`. From there, I dissected the full dataset into a separate, smaller datasets pertaining only to kit-built aircraft in the US. I did this for two reasons: 
1. I have an intense fasciation with this unique industry.
2. Because there seems to be very little analytical being done on kit sales. 

This approach improves performance, allows for focused analysis, and enables comparison between the overall aviation industry and the kit-built aircraft sector.

## Phase 1 — FAA Data Project

#### Excel Actions
• Converted FAA's `YYYYMMDD` string date format into Excel-readable format using four helper columns and then concatenating back into a single, prperly formatted date datatype. This was completed for each date-related column.
• Final formula for safety: `=IFERROR(DATE(YR, MON, DAY), "")`.
• Mapped TYPE-ENG codes to engine type categories (numerical values to text) using `=XLOOKUP()`.
• Created new column for 5-digit zip code, copied from `ZIP CODE` which was irregular and sometimes incomplete.

#### Python Actions
• Leveraged Jupyter notebooks for data exploration and processing.
• Removed unnecessary columns
• Filtered to US-based aircraft only using `df = df[df['COUNTRY'] == 'US']`
• Mapped TYPE-ACFT codes to descriptive aircraft types, numerical values to text using `=XLOOKUP()` and `=IFS()` as applicable.
• Renamed new 5-digit zip code column to `ZIP_MIN`.
• Dropped original `ZIP CODE`.
• Renamed `KIT MFR` to `KITMFG`.
• Renamed `KIT MODEL` to `KITMDL`.

*Note: The full dataset became quite slow, evenoing after flattening, so I moved to working in an ipynb (Python) which did improve performance. That said, I will likely migrate the data to a SQL DB in the near future as it become necessary.*

## Phase 2: Kit-Built Dataset Transformation

#### Python Actions
• Isolated ~16,000 U.S. kit-built aircraft from the master FAA dataset.
• Designed for faster analysis compared to the 300k+ record master dataset.
• Removed unused columns and renamed headers for clarity.
• Filtered to U.S.-based kit aircraft records.
• Cleaned `KITMFG` and `KITMDL` fields by trimming spaces, converting to uppercase, and handling missing values.
• Inserted `ACFTCAT` column mapped from `TYPE-ACFT` codes.

```
acft_mapping = {
    'O': 'Other',
    '1': 'Glider',
    '2': 'Balloon',
    '3': 'Blimp/Dirigible',
    '4': 'Fixed wing single engine',
    '5': 'Fixed wing multi engine',
    '6': 'Rotorcraft',
    '7': 'Weight-shift-control',
    '8': 'Powered Parachute',
    '9': 'Gyroplane',
    'H': 'Hybrid Lift'
}
```
• Also mapped, renamed, and repositioned `ENGCAT` and `SURFCAT` as indicated above.
• Reformatted critical date fields into consistent mm/dd/yyyy format.
• `kits.xlsx` → Final U.S.-only kit aircraft dataset, ready for reporting and analysis

## Summary
By splitting the FAA dataset into two manageable components, this workflow allows for:
• Efficient manipulation of kit-built aircraft data (~16k rows).
• Comprehensive analysis of the full 300k+ record dataset.
• Faster iteration cycles when cleaning, transforming, and visualizing data.

This log captures many of the actions I took to prepare this dataset to date and forms the foundation for future GitHub documentation. Next steps for this project includes analysis in Power BI. This repo will b e updated with that file as it becomes available.
