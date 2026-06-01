# End-to-End Data Project: Reference Guide & Toolbox

This document establishes a professional, end-to-end framework for data projects, mapping out exactly **how** to move from raw data to production-grade dashboards. 

Instead of chasing complex enterprise tools, this guide leverages a modern, lightweight, local-first stack designed to install proper software engineering disciplines from day one.

Co-written with Gemini, so excuse the language. But I promise I have supervised and have reviewed every single word it wrote :D

---

## 🏗️ The Engineering Pipeline Architecture

A professional data workflow separates tasks sequentially to ensure predictability, reproducibility, and speed. Every phase must complete its specific objective before the next begins.

```
[ Step 0: Command Center ] ──> [ Step 1: Raw Ingestion ] ──> [ Step 2: Sandbox EDA ] ──> [ Step 3: Analytics Prep ] ──> [ Step 4: Presentation Layer ]
```

**Tips:**
- use meaningful names **everywhere** - proper naming is your best documentation. The future you will curse the lazy today you :D
- don't overcomplicate - just try things out, worst case - you roll back and start again
- use an LLM as your sparring partner - they are great for fighting the "blank slate paralysis" and for brainstorming approaches. However, don't outsource the decisions to them - it's the best part of any project :)
- to be continued...
---

## 🛠️ Step-by-Step System Design

### 🏁 Step 0: The Command Center (VS Code & Git)
**Objective:** Establish environment control and state tracking before writing code or handling data.

* **How to Structure Your Workspace:**
  * Dedicate a single folder to the project and open it inside **Visual Studio Code (VS Code)**. 
  * Connect VSCode to this **Git** repository. This creates an uncorruptible timeline of your project files.
* **How to Guard Your Boundaries (.gitignore):** * Create a file named `.gitignore` at the absolute root of your project directory. 
  * Add rules to ignore data files and database binaries (e.g., write `*.csv` and `*.db` on separate lines). 
  * **The Rule:** Code belongs on GitHub; data belongs on your local machine. Never push bulk records, secret variables, or database state files to a public repository.

### 📥 Step 1: Storage & Ingestion (DuckDB)
**Objective:** We need a storage. We can use out machine or a cloud, but let's use an actual database - establish a "Raw Storage" zone.

* **How to Choose Your DB Engine:** * Avoid setting up heavy, cloud-based data warehouses or background database services for now. Use **DuckDB**, a serverless, columnar analytical engine that operates directly out of a fast file inside your project directory.
* **How to Handle Ingestion:** * Execute your ingestion script to read raw CSV files directly into DuckDB tables.
  * **The Golden Rule:** Treat this initial zone as read-only and immutable. Do not alter strings, fix types, or drop columns during ingestion. Your primary goal is ensuring an exact, untampered replica of the original source file is landed safely inside your database file.
* We can also set up an automated ingestion using API or webscraping later, for now I suggest to simply download the raw csv files onto your computer.

### 🔬 Step 2: Exploratory Data Analysis (EDA)
**Objective:** Probe data, uncover anomalies, vizualize stuff for yourself to see trends and isolate quality bugs.

* **How to Choose Your Sandbox Environment:** * Run your analysis inside a **Notebook interface** (such as VS Code's native notebook extensions, or a lightweight modern local app like **Marimo**). Notebooks provide an interactive, visual grid allowing you to execute logic cell-by-cell without re-running long backend pipeline scripts.
* **How to Run Diagnostic:** * Query your raw database tables using either Python dataframes or SQL queries. 
  * Identify data structural flaws: check the percentage of empty or null records in critical rows, look for mismatched date granularities, and flag anomalous numbers or statistical outliers. The goal is to get the feeling of the data, to know it.
* **How to Co-Pilot with LLMs:** * Use AI assistants (like ChatGPT or Gemini) as automated syntax translation engines. 
  * Paste your data schema or a small, mocked data snippet into the prompt. Ask the LLM to generate boilerplates for complex grouping strings, date parsing rules, or multi-conditional filtration syntax. Outsource memorizing code syntax and focus on interpreting the output.

### 📐 Step 3: Data Transformation & Modeling
**Objective:** Clean, format, and pre-aggregate data into static, visualization-ready tables (e.g. pivot tables in excel).

* **How to Optimize for Seamless Visualization:** * Shift all transformation computational weight *out* of the dashboard tools. A chart tool should never perform heavy arithmetic or complex data reshaping; it should simply read clean, tailored rows.
* **How to Shape the Tables:** * Write SQL transformation models or Python scripts to process your raw tables into optimized target models.
  * **Clean Rows:** Standardize capitalization, remove true duplicates, parse text formats into real dates, and cast numerical strings to real metrics.
  * **Pre-Aggregate:** If your final dashboard requires time-series trends or category distributions, write aggregation scripts to pre-pivot and compute those exact totals into standalone summary tables (e.g., total volume grouped by month and sector).

### 📊 Step 4: The Presentation Layer (BI Dashboards)
**Objective:** Vizaulize your clean, structured analytics tables.

* **How to Route Your Restructured Data:** * Connect a Business Intelligence tool to your pre-aggregated analytical tables.
* **How to Select the Right Tooling Environment:**
  * **Metabase (The Pragmatic, Open-Source Choice):** Best for developers who write SQL. It installs effortlessly, bridges data code with visual builders, and allows users to compose dashboards via raw SQL scripts or direct click-and-drag questions.
  * **Tableau (The Design-Focused Heavyweight):** Best for precision formatting, rich custom charts, and deep, interactive user-journey graphics.

---

## 📊 The Alternative Path: The Power BI Streamlined Workflow

If you execute this pipeline exclusively within **Power BI Desktop**, you must apply the exact same engineering separations in an entirely local, integrated software setup.

```
[ Local CSVs ] ──> [ Power Query Staging ] ──> [ Clean Summary Models ] ──> [ DAX Measures ] ──> [ Visual Layout ]
```

### 1. Ingesting to Staging (Power Query Engine)
* Use the **Get Data** option to connect to local source CSV files. 
* **The Workflow:** Never click the default "Load" button. Always select **Transform Data**. This routes your raw tables into the Power Query staging workspace, functioning as your isolated data preparation sandbox.

### 2. Executing EDA & Profiling Data Health
* Open the **View** tab in the top navigation ribbon and enable **Column Quality**, **Column Profile**, and **Column Distribution**. 
* Review the embedded visual health bars over your columns to instantly map data structural issues, empty values, or broken entries across your records before drawing a single chart element.

### 3. Engineering Data Transformations & Summaries
* Treat Power Query as a functional recipe builder. Every action you click (renaming columns, converting data types, filtering rows) is recorded chronologically as an automated query step.
* **How to Keep it Clean:** Filter out non-actionable or historic baseline records early. Use the **Group By** feature to build pre-pivoted, aggregated views directly in the data query stage (e.g., collapsing millions of granular detail rows into structured summaries grouped by target classification and country). 
* Click **Close & Apply** to load only these optimized tables into the visualization engine.

### 4. Isolating Calculations (DAX Measures)
* Do not drag column fields directly into visuals to generate dynamic sums, counts, or averages. This creates unmanaged, implicit dependencies.
* **How to Author Metrics:** Right-click your table, select **New Measure**, and write explicit mathematical definitions using **DAX** (Data Analysis Expressions).
  * *Example structural formula:* `Total Funding = SUM('IATI_Summary'[Aid_Budget])`
  * This guarantees your mathematical logic is written once, computed consistently, and can be reused safely across multiple charts.

### 5. Architectural Dashboard Design
* Switch to the **Report View** canvas to construct your delivery layout.
* Organize your dashboard layout matching standard natural reading patterns:
  * Place high-level macro KPI totals (using **Card** visuals) at the absolute top left.
  * Situate mid-level comparative charts (such as sorted bar graphs showing allocations by sector) in the center.
  * Position fine-grain trend timelines toward the bottom, and place configuration filters (Slicers) consistently on a single edge for interactive exploration.
