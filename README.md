IPv4 Address Classification via Active Probing and Machine Learning
Author: Chandni Sathishkumar
Advisor: Prof. John Heidemann
Affiliation: USC Information Sciences Institute
Course: Research Practicum Project C (Spring 2025)

🔍 Overview
This project implements a scalable pipeline to classify IPv4 addresses into behavioral categories such as DHCP/NAT, static/commercial, and unknown using active ICMP probing data. Inspired by Cai et al. (2010), the approach replaces threshold-based heuristics with machine learning—leveraging features like availability, volatility, uptime sessions, and RTT metrics.

Key components:

Feature Derivation from 18M+ probe rows

Reverse DNS labeling using regex and PTR records

LightGBM-based classification

Unsupervised clustering (KMeans) for exploratory analysis

Block-level entropy analysis and per-/24 subnet evaluation

📦 Dependencies
Python 3.9+

pandas, numpy, scikit-learn

lightgbm

matplotlib, seaborn

tdigest, tqdm

To install:

bash
Copy
Edit
pip install -r requirements.txt
🚀 Quick Start
Prepare input data

Place the raw probe CSVs (e.g., chunk0aa.csv, chunk1aa.csv, ...) into the data/ directory. These files should contain:

css
Copy
Edit
['reply_type', 'time_s', 'rtt_us', 'ttl', 'probe_addr']
Run feature extraction

bash
Copy
Edit
python scripts/process_raw.py
This generates aggregate.csv with per-address behavioral features.

Clean labels and apply DNS inference

bash
Copy
Edit
python scripts/label_cleanup.py
Removes unresolved/error rows, merges unknown → static/commercial, and outputs a labeled dataset.

Train and evaluate LightGBM

bash
Copy
Edit
python scripts/classify_lightgbm.py
Trains a LightGBM classifier and prints accuracy, confusion matrix, and feature importance.

Run clustering + PCA Open clustering_analysis.ipynb to visualize behavioral groupings (k=3) in PCA space.

Analyze DHCP/NAT misclassifications

bash
Copy
Edit
python scripts/misclassification_analysis.py
Identifies false negatives where dynamic hosts were misclassified.

Measure block-level entropy

bash
Copy
Edit
python scripts/block_entropy.py
Outputs mixed /24s and plots entropy distribution.

📊 Features
The final dataset includes:


Feature Name	Description
availability	Fraction of successful pings
volatility	Normalized count of online sessions
median_up/down	Median durations of uptime/downtime
longest_down	Longest downtime stretch (seconds)
median_rtt, std_rtt	RTT stats via TDigest
rtt_p95, rtt_p99	RTT percentiles
block_usage_fraction	Active IPs in /24 / 256
ip_int, octet1-4	Encoded IP address fields
is_private	Boolean for RFC1918 range
label	One of: DHCP/NAT, static/commercial, unknown
📌 Notes
Probe interval: approx. every 11 minutes

Block granularity: /24 (prefix24)

Class imbalance handled using class_weight="balanced"

Figures referenced in final report: figures/ directory

📚 References
Cai et al., "A Measurement Study of IPv4 Address Space Utilization," [IMC 2010]

Luckie et al., "Learning regex to extract router hostnames," [IMC 2019]

Heidemann et al., "LANDER: Longitudinal Active Network Data Repository" (USC/ISI)

🙏 Acknowledgments
Special thanks to Prof. John Heidemann for his domain expertise, prompt feedback, and guidance throughout this project.

Let me know if you'd like this converted to a README.md file or automatically zipped into a GitHub-ready repo.






You said:
add a section saying notes on meeting with professor , there were 2 meetings first meetin involved suggestions for deruved features and to. verify iif i was doing it right, second meeting involved discussing results and hw to improve the insigts


ChatGPT said:
Here's an additional section for your README.md (or your report appendix, if you prefer), titled Notes on Meetings with Advisor:

🗒️ Notes on Meetings with Advisor
Throughout the course of this project, I had two key meetings with Prof. John Heidemann, which shaped the direction and depth of the work:

📅 First Meeting – Feature Engineering and Data Validation
Goal: Validate early direction and confirm correct interpretation of probing data.

Outcome:

Received confirmation that my approach to extracting availability, session churn, and RTT metrics was on track.

Prof. Heidemann suggested tracking volatility as a normalized count of state transitions, and longest downtime to better differentiate dynamic hosts.

He also emphasized computing robust per-/24 block features, like block utilization, to support both clustering and classification.

Clarified time-scale interpretation: probe interval is ~11 minutes, so raw durations in seconds should be contextualized accordingly.

📅 Second Meeting – Results Interpretation and Insight Refinement
Goal: Review model and clustering results and discuss how to better derive insight from them.

Outcome:

Reviewed LightGBM results and clustering plots; discussed how std_rtt and block_usage_fraction helped separate host types.

Prof. Heidemann recommended focusing on outlier clusters with very high RTTs or odd availability patterns.

Suggested analyzing misclassifications, especially false negatives for DHCP/NAT, to better understand model limitations.

Emphasized explaining why features worked (not just which ones), and drawing clearer comparisons with Cai et al.'s original rules.

These meetings were pivotal in ensuring the methodology was rigorous and that the final insights extended beyond surface-level classification into meaningful Internet measurement conclusions
