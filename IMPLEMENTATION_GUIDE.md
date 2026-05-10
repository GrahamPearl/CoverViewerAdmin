# Cover Data Viewer - Enhanced Report System

Complete implementation of 6 specialized reports with date range filtering, pagination, charting, and extensible framework.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Files Structure](#files-structure)
3. [New Reports](#new-reports)
4. [Key Features](#key-features)
5. [Usage Guide](#usage-guide)
6. [Report Framework Pattern](#report-framework-pattern)
7. [Future Report Development](#future-report-development)

---

## 🎯 Overview

The Cover Data Viewer now includes **6 specialized reports** across 3 categories:

### **A: Teacher Absence Reports**
- **A1:** Individual Teacher Absence Profile
- **A2:** Cover Teacher Workload Analysis

### **B: Absence Pattern & Reason Reports**
- **B3:** Absence Trend Report (with holiday detection)
- **B4:** Absence Reason Analysis

### **C: Coverage & Curriculum Impact Reports**
- **C5:** Coverage Gap Report
- **C6:** Subject & Curriculum Impact Report

All reports:
- ✅ Respect currently selected date range
- ✅ Support pagination for large datasets
- ✅ Include appropriate visualizations (charts)
- ✅ Export to Excel with metadata
- ✅ Support print-friendly views
- ✅ Follow extensible framework pattern

---

## 📁 Files Structure

### **New Files**
```
viewer-reports.js         - Report definitions & framework
viewer-charts.js          - SVG chart generation utilities
```

### **Modified Files**
```
viewer.html               - Date range selectors, report modal
viewer.js                 - Date range logic, report integration
viewer-export.js          - Report export functionality
```

### **Unchanged Files**
```
viewer-config.js          - Firebase configuration
viewer-styles.css         - Styling
viewer.env                - Environment configuration
```

---

## 📊 New Reports Specification

### **A1: Individual Teacher Absence Profile**

**Purpose:** Analyze absence patterns for each teacher

**Data:**
- Total absences per teacher
- Most common absence reason
- Day-of-week pattern (which days absent most)
- Last absence date
- Sorted by frequency

**Features:**
- Horizontal bar chart (Top 10 teachers)
- Pagination (50 teachers/page)
- Exportable to Excel

**Data Structure:**
```javascript
{
  teacher: string,
  totalAbsences: number,
  lastAbsenceDate: string (YYYY-MM-DD),
  topReason: string,
  topReasonCount: number,
  mostFrequentDay: string (Sun-Sat),
  dayOfWeekPattern: { Mon: 5, Tue: 3, ... },
  allReasons: { reason: count, ... }
}
```

---

### **A2: Cover Teacher Workload Analysis**

**Purpose:** Track coverage load and subject diversity per cover teacher

**Data:**
- Periods covered per teacher
- Number of unique subjects covered
- Subject list
- Department distribution
- Average periods per subject
- Sorted by workload

**Features:**
- Horizontal bar chart (Top 10 cover teachers)
- Pagination (50 teachers/page)
- Shows subject diversity

**Data Structure:**
```javascript
{
  coverTeacher: string,
  periodsCovered: number,
  uniqueSubjects: number,
  subjects: [string],
  departmentDistribution: { subject: count, ... },
  periodDistribution: { period: count, ... },
  avgPeriodsPerSubject: number (as string, 1 decimal)
}
```

---

### **B3: Absence Trend Report**

**Purpose:** Visualize absence trends over time with holiday detection

**Data:**
- Daily absence counts
- Absence by day of week
- Weekly aggregation
- **Holiday detection:** Automatic gap identification (days with zero absences)
- Reason breakdown

**Features:**
- Line chart (absence trend over time)
- Holiday identification (displayed in table)
- Daily trend table
- Weekly summary
- No pagination (single-view report)

**Holiday Detection:**
- Identifies date ranges with no absences in selected period
- Useful for identifying school holidays and breaks
- Displayed as informational list in report

**Data Structure:**
```javascript
{
  dailyTrend: [
    { date: string, dow: string, absenceCount: number, uniqueTeachers: number },
    ...
  ],
  weeklyData: [
    { week: string (YYYY-MM-DD), absenceCount: number, recordCount: number },
    ...
  ],
  holidays: [string (YYYY-MM-DD), ...],
  reasonBreakdown: { reason: count, ... }
}
```

---

### **B4: Absence Reason Analysis**

**Purpose:** Detailed breakdown of all absence reasons

**Data:**
- All reasons with frequency
- Percentage of total
- Unique teachers citing each reason
- Most common teacher using that reason
- Date range for each reason
- Sorted by frequency

**Features:**
- Horizontal bar chart (Top 10 reasons)
- Pagination (50 reasons/page)
- Date range information for each reason

**Data Structure:**
```javascript
{
  reason: string,
  count: number,
  percentage: string (1 decimal with %),
  uniqueTeachers: number,
  topTeacher: string,
  topTeacherCount: number,
  dateRange: { first: string (YYYY-MM-DD), last: string (YYYY-MM-DD) }
}
```

---

### **C5: Coverage Gap Report**

**Purpose:** Identify absences without adequate coverage

**Data:**
- Summary metrics:
  - Total absences
  - Covered count
  - Uncovered (gap) count
  - Coverage rate (%)
- Uncovered absences list (first 20 shown)
- Multi-absence days (days with >2 absences)
- Alert indicators

**Features:**
- Summary stats box (no chart)
- Uncovered records table (highlighted in red)
- High-absence days list
- No pagination (summary view)

**Data Structure:**
```javascript
{
  summary: {
    totalAbsences: number,
    covered: number,
    uncovered: number,
    coverageRate: string (% format),
    multiAbsenceDays: number
  },
  uncoveredRecords: [record, ...],
  multiAbsenceDays: [
    { date: string, absenceCount: number, uniqueTeachers: number },
    ...
  ]
}
```

---

### **C6: Subject & Curriculum Impact Report**

**Purpose:** Analyze impact of absences on subjects and departments

**Data:**
- By subject:
  - Absence count
  - Number of affected classes
  - Unique teachers
  - Most affected class
- By department (derived from subject prefix)
- Department summary with percentages

**Features:**
- Pie chart (Absence distribution by department)
- Pagination (30 subjects/page)
- Department breakdown table
- Subject detail table

**Data Structure:**
```javascript
{
  subjects: [
    {
      subject: string,
      absenceCount: number,
      affectedClasses: number,
      uniqueTeachers: number,
      mostAffectedClass: string,
      deptEstimate: string
    },
    ...
  ],
  classes: [
    {
      className: string,
      absenceCount: number,
      affectedSubjects: number,
      subjects: [string]
    },
    ...
  ],
  deptData: { dept: count, ... }
}
```

---

## ✨ Key Features

### **Date Range Filtering**

All reports respect the date range selected in the main filter bar:

```
From Date: ____________   To Date: ____________
[7 Days] [30 Days] [All Data]
```

- Quick select buttons for common ranges
- Custom date range input
- Displays "Date range: X to Y" indicator
- All reports recalculate when date range changes

### **Pagination & Aggregation**

| Report | Type | Default Page Size |
|--------|------|------------------|
| A1     | Paginated list | 50 |
| A2     | Paginated list | 50 |
| B3     | Summary (no pagination) | N/A |
| B4     | Paginated list | 50 |
| C5     | Summary (no pagination) | N/A |
| C6     | Paginated list | 30 |

**Pagination Behavior:**
- Bottom of table shows current page
- Click page number to navigate
- Export button exports ALL pages regardless of current view
- Print view shows all data

### **Charts**

SVG-based charts render in report modal:

| Report | Chart Type | Content |
|--------|-----------|---------|
| A1 | Horizontal Bar | Top 10 absent teachers |
| A2 | Horizontal Bar | Top 10 cover teachers by workload |
| B3 | Line Chart | Daily absence trend over time |
| B4 | Horizontal Bar | Top 10 absence reasons |
| C5 | None | Summary metrics only |
| C6 | Pie Chart | Absence distribution by department |

### **Export Options**

**View Report Button:**
1. Select report from dropdown
2. Click "View Report"
3. Report opens in modal with chart + table + pagination

**Export Buttons:**
- **Excel:** Exports to .xlsx with:
  - Metadata sheet (report name, date range, record count)
  - Data sheet (full report data)
  - All pages included
  
- **Print:** Opens print view with embedded chart and formatted table

---

## 📖 Usage Guide

### **Basic Workflow**

1. **Load Data:**
   - Data automatically loads from Firebase on page load
   - Default date range: last 30 days
   - Click "🔄 Refresh" to reload

2. **Set Date Range:**
   ```
   Option A: Quick Select - Click [7 Days] [30 Days] [All Data]
   Option B: Custom Range - Enter "From Date" and "To Date"
   ```

3. **Filter by Teacher:**
   - Type teacher name in "Absent Teacher" or "Cover Teacher"
   - Click dropdown suggestion or finish typing
   - Filters apply instantly

4. **View Report:**
   - Open "⬇️ Export" modal
   - Select report from "Select Report" dropdown
   - Click "👁️ View Report"
   - Report displays in modal with chart + table + pagination

5. **Export/Print:**
   - From report modal:
     - Click "📥 Export to Excel" or "🖨️ Print Report"
   - From export modal:
     - Click "📄 Export to CSV", "📊 Export to Excel", or "🖨️ Print"

### **Report View Modal**

```
[Report Title]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Generated: [timestamp] | Records: [count] | Date Range: [range]

[Chart (if applicable)]

[Table with data]

[Pagination controls (if applicable)]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Export to Excel] [Print Report] [Close]
```

---

## 🔧 Report Framework Pattern

### **Creating a New Report**

All reports follow a consistent framework for extensibility:

```javascript
const newReport = FRAMEWORK.createReport({
  // Identification
  id: "unique-report-key",
  title: "Display Name",
  description: "What this report shows",
  category: "D", // A, B, C, D, etc.

  // Chart configuration
  hasChart: true,
  chartType: "bar", // bar | line | pie
  hasPagination: false,
  defaultPageSize: 50,

  // Required: Generate report data from filtered records
  generateData(filteredData) {
    // Process filteredData array
    // Return { title, timestamp, recordCount, data, ... }
    return {
      title: "...",
      timestamp: new Date().toLocaleString(),
      recordCount: filteredData.length,
      data: [/* ... */]
    };
  },

  // Required: Render table/data section
  renderTable(reportData, page = 1) {
    // Return HTML string with table
    return `<table>...</table>`;
  },

  // Optional: Render chart
  renderChart(reportData) {
    // Return SVG string or null
    return ViewerCharts.barChartHorizontal([...], "Title");
  },

  // Optional: Custom Excel export
  exportToExcel(reportData, schoolId, report) {
    // Custom export logic
  },

  // Optional: Custom CSV export
  exportToCSV(reportData, schoolId, report) {
    // Custom export logic
  }
});

// Register report
ViewerReports.REPORTS.push(newReport);
```

### **Framework Methods**

- `generateData(filteredData)` - Process data, return report object
- `renderTable(reportData, page)` - Return HTML table (supports pagination)
- `renderChart(reportData)` - Return SVG chart or null
- `exportToExcel(...)` - Custom export handler (optional)
- `exportToCSV(...)` - Custom export handler (optional)

### **Automatic Features**

Once you define a report with the framework:
- ✅ Automatically appears in report selector dropdown
- ✅ Respects date range filtering
- ✅ Pagination handles automatically
- ✅ Export buttons work automatically
- ✅ Print view formats automatically

---

## 🚀 Future Report Development

### **Adding Report Category D**

To add new reports for category D or beyond:

1. **Create report in `viewer-reports.js`:**
```javascript
const reportD1 = FRAMEWORK.createReport({
  id: "d1-new-report",
  title: "D1: New Report Name",
  category: "D",
  generateData(filteredData) { /* ... */ },
  renderTable(reportData, page) { /* ... */ },
  renderChart(reportData) { /* optional */ }
});

// Register immediately
ViewerReports.REPORTS.push(reportD1);
```

2. **No HTML changes needed** - Auto-appears in dropdown

3. **No viewer.js changes needed** - Existing report handler works

4. **Export works automatically** - `exportReportToExcel()` handles standard format

### **Recommended Future Reports**

**D. Weekly Performance & Trend Reports:**
- D1: Weekly Performance Summary (absence counts week-over-week)
- D2: Teacher Absence Trends (how absence patterns change over time)

**E. Compliance & Audit Reports:**
- E1: Attendance Compliance (identify excessive absences)
- E2: Audit Trail (all records with timestamps)

**F. Predictive & Insight Reports:**
- F1: Substitute Availability (who's available for cover duties)
- F2: Department Load Analysis (which departments need support)

### **Chart Types Available**

```javascript
ViewerCharts.barChartHorizontal(data, title)  // Sorted bar chart
ViewerCharts.lineChart(data, title)           // Line trend chart
ViewerCharts.pieChart(data, title)            // Pie/donut chart
```

---

## 🔗 Integration Steps

### **1. Copy Files**
```
/viewer-reports.js
/viewer-charts.js
/viewer.html (updated)
/viewer.js (updated)
/viewer-export.js (updated)
/viewer-config.js
/viewer-styles.css
/viewer.env
```

### **2. Ensure Firebase Connection**
```
Firebase SDK scripts already included in viewer.html:
- firebase-app.js
- firebase-firestore.js
```

### **3. Verify File Load Order**
```javascript
<!-- In viewer.html, IMPORTANT ORDER: -->
<script src="./firebase-adapter.js"></script>
<script src="./viewer-config.js"></script>
<script src="./viewer-export.js"></script>
<script src="./viewer-charts.js"></script>
<script src="./viewer-reports.js"></script>
<script src="./viewer.js"></script>  <!-- LAST -->
```

### **4. Test Workflow**
1. Open viewer.html in browser
2. Verify data loads
3. Test date range selectors
4. Test each of 6 reports
5. Verify pagination works
6. Test chart rendering
7. Export to Excel and verify format

---

## 🎨 Customization Guide

### **Change Default Date Range**

In `viewer.js`, `setDefaultDateRange()` function:
```javascript
function setDefaultDateRange() {
  const today = new Date();
  const thirtyDaysAgo = new Date(today.getTime() - 30 * 24 * 60 * 60 * 1000);
  // Change 30 to different number for different default range
}
```

### **Change Pagination Size**

In `viewer-reports.js`, each report definition:
```javascript
defaultPageSize: 50,  // Change to different number
```

### **Customize Chart Colors**

In `viewer-charts.js`, COLORS object:
```javascript
const COLORS = {
  primary: "#667eea",
  secondary: "#764ba2",
  chart: ["#667eea", "#764ba2", "#5a67d8", ...],
};
```

### **Modify Holiday Detection**

In `viewer-reports.js`, `reportB3.generateData()` function:
```javascript
// Current: Gaps in date data (days with no absences)
// Customize: Add manual holiday dates, use different logic, etc.
```

---

## 📋 Troubleshooting

### **Reports Not Appearing**
- Check console for JavaScript errors
- Verify all script files are in same directory
- Ensure `viewer-reports.js` loads before `viewer.js`

### **Data Not Filtering by Date**
- Check date format (should be YYYY-MM-DD)
- Verify `filterDateFrom` and `filterDateTo` values are set
- Check console: `console.log(filters)` in viewer.js

### **Charts Not Rendering**
- Verify ViewerCharts is defined before reports use it
- Check browser console for SVG errors
- Ensure chart data is in correct format

### **Export Not Working**
- Verify XLSX library is loaded (check script order)
- Check export modal is opening
- Verify report data structure matches export expectations

### **Holiday Detection Not Working**
- Verify date data exists in records
- Check that dates in selected range have corresponding records
- Try "All Data" quick select to verify holiday detection logic

---

## 📄 License & Notes

- Reports framework is extensible via FRAMEWORK.createReport()
- All data processing is client-side (no server required)
- Charts are SVG-based (no external chart library needed)
- Excel export uses XLSX library (included via CDN)
- Framework designed to support unlimited future reports

---

## 📞 Support

For issues or enhancement requests:
1. Check troubleshooting section above
2. Review Report Framework Pattern for custom reports
3. Examine existing report implementations for code examples
4. Check browser console for specific error messages

---

**Version:** 2.0 (Enhanced Reports)  
**Last Updated:** 2026-05-10  
**Status:** Production Ready
