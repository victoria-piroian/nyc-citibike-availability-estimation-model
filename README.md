# NYC CitiBike Availability Estimation Using Markov Chains

## Title & Description
This project analyzes **NYC CitiBike trip data (July 2025)** to understand ride behavior and predict **long-term bike availability at high-traffic stations**.  
Using statistical analysis and **Discrete-Time Markov Chains**, we model how bikes flow through stations during peak commuting hours to estimate the probability that a station will be full, empty, or partially occupied.

**Key Features**
- Large-scale data analysis (~5M rides)
- Ride-duration statistical modeling and conditional probability analysis
- Station-level demand characterization
- Markov Chain modeling of bike inventory dynamics
- Stationary distribution estimation for long-run availability insights

**Problem Solved**
Bike-share systems frequently suffer from **empty or full stations**, disrupting users.  
This project provides a probabilistic framework to **predict imbalance risk** and support smarter bike redistribution strategies.

---

## Installation

### Prerequisites
- Python **3.9+**
- Jupyter Notebook or Google Colab (recommended)
- Required Python libraries:
  - `pandas`
  - `numpy`
  - `matplotlib`
  - `seaborn`
  - `scipy`

### Setup Instructions

1. Clone the repository:
```
bash
git clone https://github.com/yourusername/citibike-markov-analysis.git
cd citibike-markov-analysis
```

2. Install dependencies:
```
pip install pandas numpy matplotlib seaborn scipy
```

3. Download CitiBike July 2025 trip data
This project uses publicly available CitiBike trip data.

**Source:** NYC CitiBike Open Data  
Download the dataset: https://s3.amazonaws.com/tripdata/index.html

Specifically, download the file:

```
202507-citbike-tripdata.csv.zip
```

Data Description:
- The dataset contains detailed trip-level information, including:
- Ride start and end timestamps
- Start and end station IDs and names
- Bike type (classic or electric)
- Rider category (member or casual)
- Geographic coordinates of stations

This data is used to compute ride durations, demand patterns, and transition dynamics for the Markov Chain availability model.

## Usage
1. Data Cleaning & Feature Engineering

The script:
- Computes ride duration from timestamps
- Removes invalid rides (negative duration)
- Filters extreme outliers (> 3 hours)

```
df['ride_duration'] = pd.to_datetime(df['ended_at']) - pd.to_datetime(df['started_at'])
df['ride_duration'] = df['ride_duration'].dt.total_seconds() / 60
```

2. Exploratory Statistical Analysis

We compute:
- Expected ride duration
- Variance of ride duration
- Probability of long rides (>20 min)
- Conditional probabilities by membership and bike type

Example:
```
avg_ride_duration = df['ride_duration'].mean()
prob_over_20 = len(df[df['ride_duration'] > 20]) / len(df)
```

3. Station Selection

Weekday trips are filtered to identify high-traffic stations based on arrivals + departures.

4. Markov Chain Modeling of Station Inventory

We model each station as a state-based system:

State: Number of bikes at the station
Transition: Net arrivals/departures every 5 minutes

Steps:

1. Convert trips into +1 (arrival) / −1 (departure)
2. Aggregate activity into 5-minute intervals
3. Simulate station inventory trajectory
4. Build transition probability matrix P
5. Solve for stationary distribution: 𝜋 = 𝜋𝑃

Implemented using:
```
eigvals, eigvecs = scipy.linalg.eig(P.T)
```

5. Output Interpretation
The stationary distribution gives: The long-run probability that a station has k bikes available during a time block.

This allows us to estimate:
- Risk of station saturation (no docks available)
- Risk of depletion (no bikes available)
- Operational imbalance patterns

## Author

Victoria Piroian

Cornell University

Faculty of Operations Research & Information Engineering, 2025
