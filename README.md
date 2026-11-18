# Newsroom — Power BI report

For model and measure work first, open **Newsroom_Model.pbip**. It opens a blank report bound to the same semantic model, with the same CSV sources, three relationships and 40 measures in ReportMetrics. Switch to Model view to inspect the relationships and measure folders. The full dashboard entry remains Newsroom.pbip. Both are separate report entry points to one shared semantic model.

Open **Newsroom.pbip** in a recent Power BI Desktop version that supports PBIP, PBIR and TMDL. Keep the .pbip, Newsroom.Report, Newsroom.SemanticModel and model_data folder together. Refresh after opening. Save as .pbix in Desktop if desired. This revision loads ordinary local CSVs through Binary.Buffer(File.Contents(...)); the large embedded Base64/GZip query payloads have been removed.

The DataFolder parameter defaults to this project's current absolute model_data path. When moving or extracting the ZIP elsewhere, update DataFolder in Transform data → Manage Parameters, or edit the string in Newsroom.SemanticModel/definition/expressions.tmdl before opening. No account credential or gateway is required for local Desktop refresh.

## Stream-error revision

Reported error: M Engine “Can not access a closed Stream” during project load (Power BI Desktop 2.159.1231.0). The log does not establish the precise root cause. As a mitigation, all five data partitions now use short buffered local-file queries instead of compressed inline payloads. Data values, measures and report visuals are preserved. The original ZIP is retained in the parent project's _diagnostics folder. The revised project was successfully opened in Power BI Desktop and the report visuals rendered with the local data.

## Pages

| Page | Question answered |
|---|---|
| Overview | How are we doing? |
| Content | What content works? |
| Publishing | When should we post? |
| Platforms | Which platform works? |
| Story detail (hidden) | How did this story perform? |

Navigation buttons open each main page. The funnel icon at the upper right opens the left slide-over panel; the X icon at its upper-right corner closes it. Both controls have tooltips and no visible text. Display-only bookmarks preserve data selections. Slicers for activity month, brand, platform, topic, format, publication month and observation days synchronize across pages. Use Ctrl+click for action buttons while editing in Power BI Desktop. Normal clicks work in reading mode.

The icon-only sun and moon SVG buttons select the hidden synchronized Theme slicer through bookmarks. Appearance measures control card, title, axis and table colors. Two full-canvas SVG backgrounds and two transparent footer variants are switched by the same bookmarks on all five pages. The Theme control stays hidden. Light is the default. No duplicate theme pages are used.

The 1440 × 880 layout uses one toolbar for the wordmark, four navigation buttons and utility icons. The active page has a distinct theme-aware button fill. A concise dynamic summary sits above the KPI row, followed by two chart rows. Large redundant page headings and the personal photo have been removed. Kutaiba Alaaeddin, Data & BI Analyst and k.alaaeddin@outlook.com form a transparent footer along the bottom.

Visual titles, chart legends, table headers and values are centered; subtitles are disabled. Titles use concise English, with questions only where useful.

## Drillthrough and tooltips

On Content, right-click the ArticleID/story row in “Which stories should we explore?” and drill through to “How did this story perform?”. The ArticleID binds the page to one story, and the detail page compares its platform placements and daily interaction timeline. The Overview button returns to the first page. Native chart tooltips include interaction rate, active-post count and response per active post. Metric help explains the interaction definition and synthetic-data status.

## Model

```
PublishDate[Date] 1 → * DimPost[PublishDate]
DimPost[PostID]   1 → * FactPostDaily[PostID]
DimDate[Date]     1 → * FactPostDaily[ActivityDate]
Theme            disconnected
ReportMetrics    measure table
```

DimPost combines brand, account, story, topic and format attributes. Three single-direction active relationships form the model; there are no bidirectional filters. All source identity fields stay hidden in the field list except when explicitly used in story-detail tables.

## Measure folders

01 Audience; 02 Engagement; 03 Publishing (with Cohort subfolder); 04 Traffic and Video; 05 Trends; 06 Benchmarking; 07 Answers; 08 Tooltips; 09 Appearance.

## Date and metric interpretation

- Activity month limits the days on which metrics occurred. Posts Published maps those selected days to publication dates. Active Posts counts posts represented in the daily fact for that period. The two counts answer different questions.
- Publication month filters post cohorts. Both slicers can be combined deliberately; otherwise leave the unused date role at All.
- Mature measures on Content and Publishing use posts with a complete 30-day observation window and ignore activity-date/age filters. Use Publication month to choose their cohort. Activity month continues to filter activity visuals, not the mature measures.
- The Publishing response-age chart uses activity-period counts; comparisons can be affected by differing cohort sizes. For an equal-window comparison, choose Publication month and Observed days = 30, with Activity month at All.
- Interactions = Likes + Comments + Shares + Quotes; Saves stay separate. Interaction Rate is a ratio of totals, and no measure estimates unique audience reach.
- The default synthetic data is available through 2025-12-31. Late posts have shorter observation windows. No engagement after day 29 is modeled.
- All brands, headlines and performance observations are synthetic. Any apparent trend is a simulation result, not a real-world finding.

## Validation status

The accompanying VALIDATION.json records schema and structural checks on the final files. These cover local CSVs and their buffered query definitions, relationship keys, counts, navigation targets, bookmark targets, visual bounds, centered titles and disabled subtitles. Official PBIR JSON schemas validate document structure; they do not execute DAX or guarantee that every visual-formatting property renders as intended.

Power BI Desktop was used for live visual testing. Light and dark canvases, cards, chart text, table rows, transparent footer, active navigation and cross-page theme persistence were inspected. The left filter panel opened and closed through its linked bookmarks. See LIVE_QA.md for the exact scope. Desktop-generated 2.14 visual definitions are checked in memory against the cached public 2.7 schema for common structural compatibility; this is not a full 2.14 schema certification.

## Acceptance checks in Desktop

1. Close the failed Power BI project without saving. Reopen Newsroom.pbip from this revised folder and refresh. Confirm 30,000 DimPost rows and 882,124 FactPostDaily rows, with three active relationships. For an extracted copy at another path, first update DataFolder as described above.
2. With no filters, confirm 1,109,059,490 impressions and 24,306,576 interactions.
3. Select Dark, move between pages, then open and close Filters. Confirm theme and slicer selections persist.
4. Select a brand and platform. Confirm answer cards and charts update. Check that table headers/values, titles and legends are centered and no subtitles appear.
5. On Content, choose Publication month, then drill through from a story row. Confirm the resulting page contains only that ArticleID's placements.
6. Verify maturity and activity-date interpretation above; confirm a non-video selection leaves video-only KPIs blank.

The folder is portable. The raw source and separate synthetic CSV package remain in the parent project. The versioned builder is ../build_powerbi.py.

## Reserved table-name correction

The unsupported table name Measures has been replaced by ReportMetrics. All 40 measure definitions and display folders are preserved, and all report query and conditional-formatting references point to ReportMetrics. The original reserved-name file is retained outside the semantic model in the parent _diagnostics folder.
