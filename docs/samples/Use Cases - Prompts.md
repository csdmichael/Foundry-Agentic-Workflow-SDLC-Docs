**1. AI Part Lookup Assistant**



Engineers often need information about a part but must search across multiple systems to find inventory levels, supplier details, purchase orders, and delivery dates. This process is time-consuming and delays decision-making.



The website provides a simple search box where users enter a part number. Behind the scenes, APIs retrieve data from inventory and supply chain systems, and an AI assistant summarizes the results in plain English. The user immediately sees part status, inventory availability, expected delivery dates, and recommended next steps.



**2. Supplier Risk Dashboard**



Supply chain teams need early visibility into supplier issues before they impact manufacturing operations. Today, this information is often spread across spreadsheets, emails, and planning tools.



The website displays a list of suppliers along with risk indicators such as late shipments, low inventory, or overdue purchase orders. APIs collect supplier and procurement data, while AI generates simple explanations and suggested actions for each risk. Users can quickly identify which suppliers require attention.



**3. Manufacturing Shortage Tracker**



Manufacturing teams need a quick way to understand which shortages could affect production schedules. Finding the root cause usually requires coordination across multiple departments.



The website shows active shortages and lets users drill into a specific issue. APIs return information about affected parts, suppliers, and orders, while AI explains the likely cause and business impact. The result is a single place where teams can monitor and resolve shortages faster.



**4. Engineering Knowledge Search**



Engineers frequently need to find technical documents, procedures, troubleshooting guides, and best practices. Valuable information exists but is often difficult to locate quickly.



The website provides a natural language search experience. Users can ask questions such as "How do I troubleshoot Part ABC-123?" or "Show calibration procedures for Tool XYZ." APIs retrieve relevant documents, and AI summarizes the content with links to the source materials, reducing time spent searching.



**5. Purchase Order Status Assistant**



Procurement and operations teams often need updates on open purchase orders. Checking status requires logging into multiple systems and manually reviewing records.



The website allows users to enter a purchase order number and instantly view status, supplier information, expected delivery dates, and any delays. APIs retrieve the latest procurement data, while AI generates a concise status summary and highlights orders that may require escalation or follow-up.


**6. SemiScale Alpha (or SiliconStat)

Description: 
A high-density, single-page financial analytics dashboard built to monitor the world’s top 10 semiconductor manufacturers by market capitalization. The core feature is an interactive, reactive chart and data matrix allowing users to toggle time-series performance filters seamlessly across 8 distinct intervals.

Prompt: 
You are an expert frontend developer and financial systems engineer. Build a responsive, high-performance, single-page web dashboard using React, Tailwind CSS, Lucide React icons, and Recharts.

The goal is to track the top 10 semiconductor companies by market cap: NVIDIA (NVDA), TSMC (TSM), Broadcom (AVGO), Samsung Electronics (005930.KS), ASML (ASML), SK Hynix (000660.KS), Micron Technology (MU), AMD (AMD), Applied Materials (AMAT), and Intel (INTC).

Implement the following specific features:

1. STATE MANAGEMENT & FILTERS:
- Create a prominent filter toolbar with 8 options: "Current", "Today", "Last Week", "3 Months", "6 Months", "1 Year", "YTD", and "5 Years".
- "Current" should show a real-time tracking ticker grid.
- All other filters must update a historical performance trend chart.

2. DASHBOARD LAYOUT:
- Header: Minimalist dark-mode theme ("SemiScale Alpha"), live market status indicator, and last updated timestamp.
- Hero Grid: 10 scannable asset cards displaying the company name, ticker symbol, current market cap, real-time stock price, and absolute/percentage change colored dynamically (emerald green for gains, crimson red for losses).
- Main Analytics Section: A large, clean line or area chart utilizing Recharts to show historical trends based on the active time filter.
- Data Table: A sortable data matrix showing additional metrics (24h Volume, P/E Ratio, 52-Week High/Low).

3. TECHNICAL REQUIREMENTS:
- Use Tailwind CSS to enforce a professional fintech aesthetic (Slate/Zinc dark palette, high contrast, clean typography).
- Create a mock data service layer using a static array of realistic historical data points for all 10 tickers spanning 5 years, so the filters work instantly out-of-the-box without external API keys.
- Write modular, semantic component code. Keep everything clean, scannable, and free of redundant text.
