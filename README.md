# Water_quality_monitoring-
import pandas as pd

def load_data(file_path):
    """
    Load CSV data into a DataFrame
    """
    try:
        df = pd.read_csv(file_path)
        print(f"✅ Successfully loaded data from {file_path}")
        return df
    except FileNotFoundError:
        print(f"❌ Error: File not found at {file_path}")
        return None
    except Exception as e:
        print(f"❌ An error occurred: {e}")
        return None
        import pandas as pd

def clean_data(df):
    """
    Clean the sensor data:
    1. Convert columns to appropriate data types
    2. Handle missing values by filling with NaN
    3. Validate data ranges
    """
    if df is None:
        return None
    
    # Convert to proper types
    df['ph'] = pd.to_numeric(df['ph'], errors='coerce')
    df['turbidity'] = pd.to_numeric(df['turbidity'], errors='coerce')
    df['temperature'] = pd.to_numeric(df['temperature'], errors='coerce')
    
    # Validate ranges
    df.loc[df['ph'] < 0, 'ph'] = float('nan')
    df.loc[df['ph'] > 14, 'ph'] = float('nan')
    df.loc[df['turbidity'] < 0, 'turbidity'] = float('nan')
    
    print("🧹 Data cleaning completed")
    return df
    import pandas as pd

class WaterQualityChecker:
    SAFE_PH_MIN = 6.5
    SAFE_PH_MAX = 8.5
    SAFE_TURBIDITY_MAX = 1.0
    
    def __init__(self):
        self.results = []
    
    def evaluate_row(self, row):
        """
        Evaluate water safety for a single sensor reading
        """
        sensor_id = row['sensor_id']
        location = row['location']
        ph = row['ph']
        turbidity = row['turbidity']
        
        # Check for missing values
        if pd.isna(ph):
            return {
                'sensor_id': sensor_id,
                'location': location,
                'status': 'Unsafe',
                'reason': 'missing pH'
            }
            
        if pd.isna(turbidity):
            return {
                'sensor_id': sensor_id,
                'location': location,
                'status': 'Unsafe',
                'reason': 'missing turbidity'
            }
        
        # Check pH range
        if ph < self.SAFE_PH_MIN:
            return {
                'sensor_id': sensor_id,
                'location': location,
                'status': 'Unsafe',
                'reason': 'pH too low'
            }
            
        if ph > self.SAFE_PH_MAX:
            return {
                'sensor_id': sensor_id,
                'location': location,
                'status': 'Unsafe',
                'reason': 'pH too high'
            }
            
        # Check turbidity
        if turbidity > self.SAFE_TURBIDITY_MAX:
            return {
                'sensor_id': sensor_id,
                'location': location,
                'status': 'Unsafe',
                'reason': 'turbidity too high'
            }
            
        # If all checks pass
        return {
            'sensor_id': sensor_id,
            'location': location,
            'status': 'Safe',
            'reason': ''
        }

    def evaluate(self, df):
        """
        Evaluate all rows in a DataFrame
        """
        if df is None:
            return []
        
        self.results = []
        for _, row in df.iterrows():
            self.results.append(self.evaluate_row(row))
        
        return self.results
        import os
import pandas as pd
from load_data import load_data
from clean_data import clean_data
from evaluate import WaterQualityChecker

def main():
    # File paths
    current_dir = os.path.dirname(os.path.abspath(__file__))
    data_path = os.path.join(current_dir, '..', 'data', 'sensor_data.csv')
    output_path = os.path.join(current_dir, '..', 'results.csv')
    
    print("💧 Starting Water Quality Monitoring System")
    
    # Load data
    df = load_data(data_path)
    if df is None:
        return
    
    # Clean data
    cleaned_df = clean_data(df)
    
    # Evaluate water quality
    checker = WaterQualityChecker()
    results = checker.evaluate(cleaned_df)
    
    # Print results
    safe_count = 0
    unsafe_count = 0
    
    print("\n🔍 Water Quality Results:")
    for result in results:
        status_symbol = "✅" if result['status'] == 'Safe' else "❌"
        reason = f"({result['reason']})" if result['reason'] else ""
        print(f"Sensor {result['sensor_id']} at {result['location']}: {status_symbol} {result['status']} {reason}")
        
        if result['status'] == 'Safe':
            safe_count += 1
        else:
            unsafe_count += 1
    
    # Print summary
    print(f"\n📊 Summary: {safe_count} safe, {unsafe_count} unsafe")
    
    # Save to CSV (Bonus)
    results_df = pd.DataFrame(results)
    results_df.to_csv(output_path, index=False)
    print(f"\n💾 Results saved to {output_path}")

if __name__ == "__main__":
    main()
    # Water Quality Monitoring System

This project processes water sensor data to determine water safety based on pH and turbidity levels.

## Features

- Loads sensor data from CSV files
- Cleans and validates sensor readings
- Evaluates water safety based on predefined thresholds
- Generates detailed safety reports
- Saves results to CSV

## Requirements

- Python 3.7+
- pandas

## Setup

```bash
# Clone repository
git clone https://github.com/your-username/Water_Quality_Monitoring.git
cd Water_Quality_Monitoring

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate  # Windows

# Install dependencies
pip install -r requirements.txt
    
