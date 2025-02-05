# ImpactFrame Extension Documentation

*Version 1.0 – P6/P9 Open Research Final Deliverable*

---

## Table of Contents

1. [Introduction](#introduction)
2. [Installation & Setup](#installation--setup)
3. [Overview](#overview)
   - [Purpose](#purpose-of-impactframe)
   - [Key Features](#key-features)
   - [System Requirements](#system-requirements)
4. [Architecture & Components](#architecture--components)
   - [High-Level Architecture](#high-level-architecture)
   - [Folder Structure Overview](#folder-structure-overview)
5. [External Dependencies](#external-dependencies)
6. [Code Structure & Key Modules](#code-structure--key-modules)
   - [Main UI & Orchestration](#main-ui--orchestration)
   - [Core Libraries and Utilities](#core-libraries-and-utilities)
   - [EPD Data & GPT Integration](#epd-data--gpt-integration)
7. [User Interface (XAML)](#user-interface-xaml)
8. [Configuration & JSON Files](#configuration--json-files)
9. [Execution Flow](#execution-flow)
10. [Future Extensions & Customization](#future-extensions--customization)
11. [Troubleshooting & FAQs](#troubleshooting--faqs)
12. [Glossary](#glossary)
13. [Appendix A. Code Listings](#appendix-a-code-listings)

---

## 1. Introduction <a name="introduction"></a>

**ImpactFrame** is a custom Revit extension developed to assist architects, engineers, and sustainability specialists in evaluating, comparing, and optimizing the environmental performance of wall assemblies. By integrating Environmental Product Declaration (EPD) data and leveraging AI through the ChatGPT API, ImpactFrame provides real-time assessments and sustainable material suggestions—all from within the Revit environment.


<div style="page-break-before: always;"></div>

## 2. Installation & Setup <a name="installation--setup"></a>

### Prerequisites

- **Autodesk Revit**: Version 2023 or later (tested primarily on Revit 2023).
- **pyRevit**: Version 4.8 or later must be installed.
- **IronPython**: Version 2.7 (as provided with pyRevit).
- **OpenAI GPT API Access**: You need a valid API key; see [OpenAI’s API Documentation](https://openai.com/api/) for details.
- **Operating System**: Windows 10 or newer.

### Installation Steps

1. **Download the Extension**:  
   - Obtain the ImpactFrame extension package (folder named `ImpactFrame.extension`).

2. **Install via pyRevit**:  
   - Place the `ImpactFrame.extension` folder into your pyRevit extensions folder.
   - Restart Revit to load the new tab and buttons.

3. **Configure API Keys**:  
   - Open the file `\lib\config.py` and insert your OpenAI API key into the `OPENAI_API_KEY` variable.
   - If using ECO Platform services, enter the token in `ECO_PLATFORM_API`.

4. **Verify Installation**:  
   - Launch Revit and open a project.  
   - Select a wall and click the ImpactFrame button on the custom tab to ensure the extension loads and displays the dashboard.

<div style="page-break-before: always;"></div>

## 3. Overview <a name="overview"></a>

### Purpose of ImpactFrame <a name="purpose-of-impactframe"></a>

ImpactFrame is designed to provide sustainability guidance during the design process by integrating:
- Real-time environmental performance assessments.
- AI-driven suggestions for more sustainable wall assemblies.
- Side-by-side comparisons of environmental impacts.

### Key Features <a name="key-features"></a>

- **GPT-Based Material Suggestions**: Proposes sustainable alternatives based on project context and EPD data.
- **EPD Comparison & Visualization**: Visualizes environmental impacts (e.g., global warming potential, water usage) using customizable charts.
- **Automated Wall Creation**: Seamlessly creates new Revit wall types reflecting AI suggestions.
- **JSON Export/Import**: Save and load wall assembly definitions for team sharing.
- **Built-In Reporting & Insights**: Generates plain-text or JSON reports summarizing impacts and recommendations.

### System Requirements <a name="system-requirements"></a>

- **Autodesk Revit**: 2023 or later.
- **pyRevit**: 4.8+.
- **IronPython**: 2.7.
- **OpenAI GPT API Access**: Valid API key required.
- **Operating System**: Windows 10 or newer.

<div style="page-break-before: always;"></div>

## 4. Architecture & Components <a name="architecture--components"></a>

### High-Level Architecture <a name="high-level-architecture"></a>

1. **User Interaction**:  
   - The user selects a wall in Revit, and the dashboard (built in WPF via `ImpactFrameDashboard.xaml` and `script.py`) extracts wall layer data.
2. **EPD Material Data**:  
   - A curated library of EPD JSON files is stored in `\lib\EPD\EPD_Data`, organized by material category.
3. **GPT Integration**:  
   - When requested, a GPT prompt is generated that includes the current wall assembly data and allowed EPD materials. The GPT API returns suggestions for a more sustainable design.
4. **Comparison & Export**:  
   - The extension compares the environmental impacts of the original and GPT-proposed assemblies, exporting data as JSON.
5. **Revit Material Creation**:  
   - The plugin automatically creates new wall types in Revit based on user selections between the original and GPT assemblies.


## 5. External Dependencies <a name="external-dependencies"></a>

### Python Standard Libraries

- **os, sys, json, datetime, io, re, collections** – For file I/O, JSON parsing, and utility functions.

### Revit API & pyRevit

- **pyRevit**:  
  - `pyrevit.forms` for UI components.
  - `pyrevit.revit` for accessing the active Revit document.
- **Autodesk Revit API**:  
  - Classes such as `Transaction`, `WallType`, `Material`, etc.

### .NET References (for WPF)

- **PresentationFramework, PresentationCore, WindowsBase, System.Xaml**

### OpenAI GPT API

- **gpt_client.py** uses either the `requests` or `openai` Python library to call the GPT API.
- **API Key**: Stored in `config.py`.

<div style="page-break-before: always;"></div>

### Folder Structure Overview <a name="folder-structure-overview"></a>

```
ImpactFrame.extension
├── hooks
│   └── command-before-exec[ID_FILE_IMPORT].py          (Optional Revit hook)
├── ImpactFrame.tab
│   ├── bundle.yaml                                     (pyRevit tab definition)
│   └── Main.panel
│       └── ImpactFrameDashboard.pushbutton
│           ├── bundle.yaml                             (pushbutton definition)
│           ├── icon.png
│           ├── ImpactFrameDashboard.xaml               (UI layout)
│           ├── script.py                               (Main dashboard logic)
│           └── log/                                    (Runtime log files)
└── lib
    ├── gpt_client.py                                   (GPT API call manager)
    ├── project_location.py                             (Retrieves Revit project lat/lon)
    ├── utils.py                                        (Utility functions)
    ├── material_graphics.py                            (Revit material creation and wall type creation)
    ├── domain_functions.py                             (Wall/EPD logic and prompt building)
    ├── epd_logic.py                                    (EPD data loading, prompt creation, JSON parsing)
    ├── data_visualization.py                           (Drawing EPD comparison charts)
    ├── json_to_impact_values.py                        (Parsing EPD text to numeric LCIA values)
    ├── config.py                                       (API keys and configuration)
    ├── parse_epd.py                                    (Raw EPD JSON parsing)
    ├── bulk_parse_epd.py                               (Batch EPD processing)
    └── EPD
        ├── EPD_Data/                                   (Folders for each material category)
        │   └── [Category folders]
        │       ├── 000_material_names.json             (UUID-to-name mappings)
        │       ├── selected_material.json              (Selected EPD for the category)
        │       └── [UUID].json                         (Individual EPD files)
        ├── all_selected_materials.json                 (Aggregated EPD selections)
        └── [Other EPD utility scripts]
    └── exported                                        (User-exported JSON files)
        ├── assembly_impacts_comparison.json
        └── indicators_min_max_values.json
```

<div style="page-break-before: always;"></div>

## 6. Code Structure & Key Modules <a name="code-structure--key-modules"></a>

### Main UI & Orchestration <a name="main-ui--orchestration"></a>

- **ImpactFrameDashboard.xaml & script.py**  
  - Implements the WPF UI and event handlers for tab navigation, GPT interactions, logging, and Revit modifications.
  - Manages tasks such as reading wall layer data, calling GPT for sustainable suggestions, and exporting impact comparisons.

### Core Libraries and Utilities <a name="core-libraries-and-utilities"></a>

- **utils.py**: Contains helper functions (e.g., unit conversions, string sanitization, unique naming).
- **project_location.py**: Retrieves project location coordinates from Revit.
- **config.py**: Holds API keys and other configuration constants.

### EPD Data & GPT Integration <a name="epd-data--gpt-integration"></a>

- **epd_logic.py**:  
  - Loads EPD data from the library.
  - Builds GPT prompts for sustainable material mapping.
  - Parses GPT responses into structured data.
- **parse_epd.py & bulk_parse_epd.py**:  
  - Process raw EPD JSON files into standardized LCIA result structures.
- **json_to_impact_values.py**:  
  - Translates textual EPD report lines into numeric impact values and exports comparison JSON.
- **gpt_client.py**:  
  - Wraps the OpenAI GPT API call with error handling and exponential backoff.
- **material_graphics.py**:  
  - Handles Revit-specific material creation and assigns visual properties (color, fill patterns).
- **domain_functions.py**:  
  - Implements the business logic for wall layer extraction and mapping Revit data to EPD/GPT prompts.
- **data_visualization.py**:  
  - Uses WPF Canvas to draw side-by-side line charts comparing environmental impacts.

<div style="page-break-before: always;"></div>

## 7. User Interface (XAML) <a name="user-interface-xaml"></a>

The ImpactFrame UI is built with WPF using a custom-designed XAML window:

- **Window Layout**:
  - **Borderless Window**: Custom chrome with `WindowStyle="None"` and `AllowsTransparency="True"` for a modern look.
  - **Custom Title Bar**: Includes a draggable header and a custom close (`X`) button.
  - **Sidebar Navigation**: A vertical panel with styled buttons (using `NavButton` style) for tabs such as Overview, Guide, Analysis, Reports, Export, and About.
  - **Content Area**: Multiple grids (one per tab) are shown/hidden based on user selection.
  
- **Design Elements**:
  - **ModernButton Style**: For primary actions.
  - **ThinScrollBar Style**: For a clean look in scrollable areas.
  - **Data Templates**: For displaying wall layer data and export options.

---

## 8. Configuration & JSON Files <a name="configuration--json-files"></a>

### Key Configuration Files

- **`config.py`**:  
  - Contains API keys (`OPENAI_API_KEY`, `ECO_PLATFORM_API`).

### JSON Data Files

- **EPD Library Files** (under `\lib\EPD\EPD_Data`):
  - Each material category folder contains:
    - `000_material_names.json` – Maps UUIDs to product names.
    - `selected_material.json` – The best match for that category.
    - Individual `<UUID>.json` files – Raw EPD data.
- **Aggregated Files**:
  - `all_selected_materials.json` – Consolidates selected materials from all categories.
- **Exported Files** (under `\lib\exported`):
  - `assembly_impacts_comparison.json` – Contains the numerical environmental impact comparisons.
  - `indicators_min_max_values.json` – Contains normalized ranges for LCIA indicators.



<div style="page-break-before: always;"></div>

## 9. Execution Flow <a name="execution-flow"></a>

### Startup Sequence

1. **Launch**:  
   - The user clicks the ImpactFrame button in Revit, which invokes `script.py` and opens the WPF dashboard.
2. **Initialization**:
   - The dashboard loads the XAML, initializes logging, retrieves the current project location, and populates the UI with data from the selected wall.
3. **User Interaction**:
   - The user navigates between tabs (Overview, Guide, Analysis, Reports, Export) using the sidebar.
   - Each tab provides specific functionality (e.g., calling GPT for material suggestions, comparing EPD data, generating reports).

### GPT Interaction Flow

1. **Request**:  
   - In the Guide tab, the user clicks “Get GPT Suggestion.”
2. **Processing**:
   - A prompt is built (using `build_epd_prompt` in domain_functions.py) and sent via `gpt_client.py`.
3. **Response & Display**:
   - The GPT response is parsed (using `parse_gpt_epd_json_response`) and the suggested wall assembly is displayed for comparison with the original.

### EPD Comparison & Wall Creation

1. **Analysis & Export**:
   - The Analysis tab allows users to compare the environmental impacts (via `json_to_impact_values.py`), displaying results on a WPF Canvas using `data_visualization.py`.
2. **Finalization**:
   - In the Export tab, the user chooses which layers (Original vs. GPT) to keep.
   - The new wall type is created in Revit using `material_graphics.py` and assigned to the selected wall.

<div style="page-break-before: always;"></div>

## 10. Future Extensions & Customization <a name="future-extensions--customization"></a>

- **Expanding EPD Categories**:  
  - Support for new material types (e.g., innovative or bio-based products) can be added by updating the EPD library.
- **UI Enhancements**:  
  - Consider interactive charts, side-by-side comparison tables, or embedded video walkthroughs.
- **Advanced Life-Cycle & Cost Analysis**:  
  - Integration of further LCA stages or cost data for deeper decision support.
- **Batch Processing**:  
  - Enable analysis of multiple walls at once for larger projects.
- **GPT Model Options**:  
  - Allow users to select between GPT-4, GPT-3.5, or other models.

---

## 11. Troubleshooting & FAQs <a name="troubleshooting--faqs"></a>

1. **No Wall Selected Error**:  
   - Ensure you have a single wall selected in Revit before launching ImpactFrame.
2. **GPT Response Parsing Issues**:  
   - If GPT returns non-JSON text, adjust the system prompt to enforce strict JSON formatting or lower the temperature.
3. **Missing EPD Files**:  
   - Verify that all UUIDs referenced in `all_selected_materials.json` exist in the `EPD_Data` folders.
4. **Material Creation Failures**:  
   - Check logs for duplicate or invalid material names; the extension attempts to sanitize and ensure uniqueness.
5. **Performance**:  
   - Large EPD libraries or multiple GPT calls may slow the extension. Consider caching frequently used data (e.g., min/max indicators).

---

## 12. Glossary <a name="glossary"></a>

- **EPD (Environmental Product Declaration)**: A standardized document detailing the environmental impact of a product.
- **LCIA (Life Cycle Impact Assessment)**: The phase of LCA that quantifies environmental impacts.
- **GWP (Global Warming Potential)**: A measure of the heat-trapping ability of greenhouse gases.
- **ADP_fossil**: Indicator for fossil resource depletion.
- **Revit CompoundStructure**: The multi-layer structure (e.g., wall) in Revit.
- **GPT**: Refers to OpenAI’s Generative Pre-trained Transformer language models (e.g., GPT-4).

<div style="page-break-before: always;"></div>

## 13. Appendix A. Code Listings <a name="appendix-a-code-listings"></a>

- **`script.py`**: The main UI logic and event handlers.  
- **`epd_logic.py`**: Core EPD data processes—loading and mapping to GPT, plus JSON parsing.  
- **`json_to_impact_values.py`**: Numeric impact computations for wall assemblies, exporting comparisons.  
- **`material_graphics.py`**: Revit material creation, layer function mapping, new wall type construction.  
- **`domain_functions.py`**: High-level domain logic for extracting wall layers, building EPD prompts, and more.  
- **`data_visualization.py`**: Renders EPD comparisons in a line chart on a WPF canvas.  
- **`gpt_client.py`**: Handles GPT API calls, retries, and partial response validation.  
- **`project_location.py`**: Fetches project latitude/longitude from Revit’s site location.  
- **`utils.py`**: Utility functions for name sanitizing, mm-to-feet conversion, and timestamping.  
- **`parse_epd.py`**: Low-level parsing of raw EPD JSON into standardized LCIA, resource, and waste data.  
- **`config.py`**: Stores user-defined constants like OpenAI API key and optional ECO platform token.  
- **`bulk_parse_epd.py`**: Offline script to parse all materials in `all_selected_materials.json` and build ready-to-use EPD data.
