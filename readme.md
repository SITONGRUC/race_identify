# Methodology: Race and Ethnicity Inference in Workforce Data

## Project Overview
This repository documents the technical approach used to infer race and ethnicity from large-scale professional profile data (e.g., LinkedIn). The methodology employs a hybrid approach, utilizing both name-based classifiers and computer vision (profile pictures) to maximize accuracy across different demographic groups.

This approach is based on a review of recent literature, including *LinkedOut?* (Evsyukova et al.), *The Missing Link?* (Wang et al.), and the *Organizational Diversity Database*.

## Methodology

### 1. Data Sources and Logic
Our review of the literature indicates that relying on a single data source is insufficient. We utilize a split-methodology depending on the target demographic:

| Target Group | Primary Signal | Secondary Signal | Rationale |
| :--- | :--- | :--- | :--- |
| **Hispanic** | **Name** | Picture | Names provide a distinct signal for Hispanic classification; picture probability is often less definitive. |
| **Asian** | **Name** | Picture | Census name data is highly predictive for Asian surnames. |
| **Black** | **Picture** | Name | Computer vision (DeepFace) provides bimodal/definitive probabilities; name data is less reliable. |
| **White** | **Picture** | Name | Similar to above, visual identification is the primary reliable signal. |

### 2. Algorithmic Workflow

#### A. Name-Based Inference (Asian & Hispanic)
* **Source:** U.S. Census Bureau data (race shares of last names).
* **Logic:** The model calculates the probability of a name belonging to a specific group based on census frequency.
* **Tools:**
    * **predictrace (R):** Used for all name-based operations regarding gender and race, as established in the *LinkedOut?* methodology.
    * **rethnicity (R) / gender (R):** Alternative packages cited in related literature for measuring ethnic composition.

#### B. Image-Based Inference (Black & White)
* **Tools:**
    * **DeepFace (Python):** Utilizing the **VGG-Face** model and "FairFace" training datasets to obtain information on race, age, and gender.
    * **OpenCV:** Used for initial face detection and image processing.
* **Scale:** Methodologies have been validated on datasets exceeding 200 million images.

#### C. Conflict Resolution
Following the protocol in *The Missing Link?*, we apply a tiered decision logic when both name and picture data are available:

1.  **Step 1 (Primary):** If a profile picture is available, calculate the probability (primarily for Black/White classification).
2.  **Step 2 (Secondary):** If the picture is missing, fallback to name-based probability.
3.  **Step 3 (Resolution):** If the picture classification is highly uncertain (no group >50% probability) **AND** the name classification is highly certain (>90%), defer to the name.

### 3. Validation
* **Manual Backup:** For low-confidence scores or failure to detect a face, the workflow includes a "human-in-the-loop" process using crowdsourcing (e.g., Amazon Mechanical Turk) to validate the visual categorization.

## Limitations & Exclusions
* **Native Americans:** Excluded from the current dataset. Neither names nor images were found to be reliable identifiers for this specific group in current academic models.
* **Hispanic Classification:** "Hispanic" is treated as a mutually exclusive category to ensure unique user counts, aligning with EEO reporting standards rather than the U.S. Census definition (where Hispanic is an ethnicity distinct from race).

---

## References

**Literature & Working Papers**
* [1] Evsyukova, Y., Rusche, F., & Mill, W. (2025). LinkedOut? A Field Experiment on Discrimination in Job Network Formation. *The Quarterly Journal of Economics*, 140(1), 283-334.
* [2] Edelman, B., Luca, M., & Svirsky, D. (2017). Racial Discrimination in the Sharing Economy: Evidence from a Field Experiment. *American Economic Journal: Applied Economics*, 9(2), 1-22.
* [3] Wang, Y., Shin, J., & Zhang, C. (2025). *The Value of Internal Labor Markets: Evidence from LinkedIn Profiles and U.S. Inventors*. [Working Paper].
* [4] Taigman, Y., Yang, M., Ranzato, M., & Wolf, L. (2014). DeepFace: Closing the Gap to Human-Level Performance in Face Verification. *Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR)*.

**Software & Data**
* Kaplan, J. (2022). *predictrace: Predict the Race and Gender of a Person from Their Name* [R package].
* U.S. Census Bureau. (2022). *Decennial Census Data on Last Names and Race*. U.S. Department of Commerce.
