# Figma Plugins Historical Data Archive

A comprehensive historical data repository for Figma Community plugins, automatically collected and stored on a daily basis for research and analysis purposes.

> **⚠️ LEGAL NOTICE**: All data in this repository belongs to **Figma, Inc.** and respective plugin creators. This archive is for legitimate research and educational purposes only. **Illegal use is strictly prohibited.**

## 📊 Project Overview

This project maintains a historical archive of Figma plugin data from the Figma Community. The data is systematically collected daily using automated scraping techniques and stored in organized JSON files for trend analysis, market research, and ecosystem monitoring.

### Key Features
- **Daily Data Collection**: Automated daily snapshots of the Figma plugin ecosystem
- **Historical Tracking**: Monitor plugin evolution, popularity trends, and creator activities over time
- **Comprehensive Metadata**: Detailed plugin information including stats, creators, publishers, and technical details
- **API-Based Collection**: Uses official Figma API endpoints for reliable data access

## 🔧 Data Collection Methodology

### API Endpoint
The project utilizes Figma's public API to collect plugin information:

```
https://www.figma.com/api/resources?sort_by=all_time&price=all&resource_type=plugin&caller=category&page_size=50&include_content=true&category=editing-effects
```

### Collection Parameters
- **sort_by**: `all_time` - Sorted by overall popularity
- **resource_type**: `plugin` - Specifically targeting plugins
- **page_size**: `50` - Optimal batch size for processing
- **include_content**: `true` - Includes detailed plugin metadata
- **price**: `all` - Includes both free and paid plugins

### Automated Collection with Firecrawl

The data collection process is automated using **[Firecrawl](https://www.firecrawl.dev/referral?rid=E8DQXRGY)**, which enables:
- **Batch Processing**: Efficient collection of large datasets
- **Rate Limiting**: Respects API rate limits and terms of service
- **Error Handling**: Robust error recovery and retry mechanisms
- **Data Validation**: Ensures data integrity and completeness

## 🗂️ Data Organization

### Directory Structure
```
figma-data/
├── 20250606/          # Data collected on June 6, 2025
│   ├── figma_plugins_20250606_134439_page_001.json
│   ├── figma_plugins_20250606_134439_page_002.json
│   └── ...
├── 20250607/          # Data collected on June 7, 2025
│   └── ...
└── README.md
```

### File Naming Convention
```
figma_plugins_{YYYYMMDD}_{HHMMSS}_page_{NNN}.json
```
- `YYYYMMDD`: Collection date
- `HHMMSS`: Collection time (UTC)
- `NNN`: Page number (zero-padded)

### JSON Data Structure
Each file contains paginated results with the following schema:

```json
{
  "session_id": "figma_plugins_20250606_134439",
  "page_number": 1,
  "timestamp": "2025-06-06T13:44:39.000Z",
  "plugin_count": 50,
  "has_more": true,
  "next_page_url": "...",
  "plugins": [
    {
      "id": "plugin-uuid",
      "name": "Plugin Name",
      "description": "Plugin description",
      "view_count": 1500,
      "like_count": 45,
      "install_count": 230,
      "user_count": 180,
      "created_at": "2024-12-15T10:30:00.000Z",
      "creator": {
        "id": "creator-id",
        "handle": "creator-handle",
        "name": "Creator Name"
      },
      "publisher": {
        "id": "publisher-id",
        "name": "Publisher Name",
        "follower_count": 1200
      },
      "category_id": "category-uuid",
      "editor_types": ["design"],
      "publishing_status": "approved_public"
    }
  ]
}
```

## 📈 Historical Analysis Capabilities

### Time Series Analysis
```python
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
import glob

def analyze_plugin_trends():
    """Analyze plugin popularity trends over time"""
    daily_data = {}
    
    # Load data from multiple days
    for date_dir in glob.glob("202*"):
        date = datetime.strptime(date_dir, "%Y%m%d")
        plugins = load_daily_data(date_dir)
        daily_data[date] = len(plugins)
    
    # Plot trend
    dates = sorted(daily_data.keys())
    counts = [daily_data[date] for date in dates]
    
    plt.figure(figsize=(12, 6))
    plt.plot(dates, counts, marker='o')
    plt.title('Daily Plugin Count Trend')
    plt.xlabel('Date')
    plt.ylabel('Number of Plugins')
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()
```

### Plugin Lifecycle Tracking
```python
def track_plugin_lifecycle(plugin_id):
    """Track a specific plugin's metrics over time"""
    plugin_history = []
    
    for date_dir in sorted(glob.glob("202*")):
        daily_plugins = load_daily_data(date_dir)
        plugin_data = next((p for p in daily_plugins if p['id'] == plugin_id), None)
        
        if plugin_data:
            plugin_history.append({
                'date': date_dir,
                'view_count': plugin_data['view_count'],
                'like_count': plugin_data['like_count'],
                'install_count': plugin_data.get('content', {}).get('plugin', {}).get('install_count', 0)
            })
    
    return pd.DataFrame(plugin_history)
```

## 🛠️ Setup and Usage

### Prerequisites
```bash
pip install pandas numpy matplotlib seaborn requests firecrawl-py
```

### Loading Historical Data
```python
import json
import glob
from datetime import datetime

def load_daily_data(date_folder):
    """Load all plugins from a specific day"""
    all_plugins = []
    pattern = f"{date_folder}/figma_plugins_*.json"
    
    for file_path in sorted(glob.glob(pattern)):
        with open(file_path, 'r', encoding='utf-8') as f:
            data = json.load(f)
            all_plugins.extend(data['plugins'])
    
    return all_plugins

def load_date_range(start_date, end_date):
    """Load plugins data for a date range"""
    date_range_data = {}
    
    for date_dir in glob.glob("202*"):
        date = datetime.strptime(date_dir, "%Y%m%d")
        if start_date <= date <= end_date:
            date_range_data[date] = load_daily_data(date_dir)
    
    return date_range_data
```

## 📊 Research Applications

### Market Analysis
- **Plugin Ecosystem Growth**: Track the overall growth of the Figma plugin marketplace
- **Category Trends**: Monitor which plugin categories are gaining popularity
- **Creator Success Patterns**: Identify successful plugin creators and their strategies

### Competitive Intelligence
- **Feature Analysis**: Track what features are trending in new plugins
- **Pricing Strategies**: Monitor monetization patterns and pricing trends
- **User Engagement**: Analyze like-to-view ratios and installation patterns

### Academic Research
- **Platform Economics**: Study marketplace dynamics and creator economics
- **Innovation Patterns**: Track innovation cycles in design tools
- **Community Behavior**: Analyze social aspects of the design tool ecosystem

## 🔄 Data Collection Automation

### Collection Schedule
- **Frequency**: Daily collection at 13:44 UTC
- **Coverage**: All public plugins in the Figma Community
- **Retention**: Historical data preserved indefinitely

### Monitoring and Quality Assurance
- **Data Validation**: Automated checks for data completeness and consistency
- **Error Logging**: Comprehensive logging of collection issues
- **Backup Strategy**: Regular backups to ensure data preservation

## ⚠️ Important Considerations

### Data Usage Guidelines
1. **Data Ownership**: All data belongs to Figma, Inc. and respective plugin creators
2. **Legal Compliance**: Usage must comply with Figma's Terms of Service and local laws
3. **Research Only**: Data is strictly for legitimate research and educational purposes
4. **No Illegal Use**: **Absolutely prohibited** for any illegal, harmful, or malicious activities
5. **Rate Limiting**: Original collection respects Figma's API rate limits
6. **Privacy**: Only publicly available information is collected
7. **Research Ethics**: Data should be used responsibly with proper attribution

### Data Limitations
- **Point-in-Time Snapshots**: Data represents specific moments, not real-time status
- **API Changes**: Data structure may vary if Figma updates their API
- **Plugin Lifecycle**: Plugins may be unpublished or modified between collections

## 📄 License and Attribution

### Data Ownership and Rights
**IMPORTANT**: All plugin data contained in this repository belongs to **Figma, Inc.** and the respective plugin creators. This repository serves only as a historical archive for research purposes.

- **Data Owner**: Figma, Inc. and individual plugin creators
- **Original Source**: Figma Community (https://www.figma.com/community)
- **Archive Purpose**: Research and educational analysis only

### Legal Usage Requirements
This dataset is provided for **legitimate research and educational purposes only**. Users must:

- **Respect Data Ownership**: Acknowledge that all data belongs to Figma, Inc. and plugin creators
- **No Commercial Use**: Do not use this data for commercial purposes without proper authorization
- **No Illegal Activities**: **STRICTLY PROHIBITED** to use this data for any illegal, malicious, or harmful purposes
- **Terms Compliance**: Comply with Figma's Terms of Service and Community Guidelines
- **Academic Citation**: Cite this repository and acknowledge Figma as the data source in publications
- **Data Protection**: Ensure compliance with applicable data protection regulations (GDPR, CCPA, etc.)

### Prohibited Uses
The following activities are **strictly forbidden**:
- Using data for spamming, harassment, or malicious contact of creators
- Attempting to reverse-engineer Figma's systems or APIs
- Commercial exploitation without proper licensing
- Any activity that violates Figma's Terms of Service
- Redistribution of data without proper attribution
- Using data to compete unfairly with Figma or plugin creators

## 🤝 Contributing

### Reporting Issues
- Data inconsistencies or collection errors
- Suggestions for additional metadata collection
- Improvements to analysis tools

### Enhancement Requests
- Additional API endpoints to track
- New analysis methodologies
- Data export formats

## 📞 Contact

For questions about the dataset, collection methodology, or collaboration opportunities, please open an issue in this repository.

---

**Data Collection Method**: Automated via Firecrawl + Figma API  
**Update Frequency**: Daily  
**Data Format**: JSON  
**First Collection**: June 6, 2025  
**Total Historical Coverage**: Ongoing daily snapshots
