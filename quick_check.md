### 1. Proposal: Adopt a "Cascading" Hybrid Model

**The Insight:** Single-method inference is prone to specific failures (e.g., *DeepFace* struggles with Hispanic identification; *BISG* struggles with Black identification).
**The Proposal:** We should not rely on one tool. Instead, we should build a decision hierarchy similar to the *Corporate Workforce* paper:

* **Step A:** Use **Images** as the primary signal for identifying **Black** individuals (where names are weak).
* **Step B:** Use **Names** as the primary signal for identifying **Hispanic** and **Asian** individuals (where phenotypes can be ambiguous).
* **Step C:** Only accept a classification if the confidence score is high (e.g., >90%). If there is a conflict between Name and Image, flag for manual review.

### 2. Proposal: Implement a Specific "South Asian" Filter

**The Insight:** A major recurring error in the literature (e.g., *Venture Funding* paper) is that image algorithms frequently misclassify darker-skinned South Asian individuals as Black.
**The Proposal:** We must run a **pre-processing step** using a name classifier to identify and exclude (or separately categorize) South Asian surnames. This will significantly reduce our "False Positive" rate for Black categorization.

### 3. Proposal: Create a "Ground Truth" Validation Sample

**The Insight:** The *Regulatory Arbitrage* and *Venture Funding* papers showed that algorithms can have error rates as high as 40%. We cannot blindly trust the output.
**The Proposal:** We should budget time to manually review a random sample of our data (e.g., **500–1,000 profiles**) to establish our own error rate.

* *Why this helps:* It allows us to report a "Precision/Recall" score in our final paper, defending us against critics who might question our data quality.



| Study Context | Primary Data | Algorithms Used | The "Inference Problem" (Key Errors) | The "Fix" (Mitigation Strategy) |
|---|---|---|---|---|
| Venture Funding | LinkedIn Photos | DeepFace + Name Embeddings | Skin-tone analysis misclassified South Asians as Black and light-skinned Black founders as White. | 100% manual review of 150k photos; checked "secondary signals" (HBCUs, affinity groups). |
| Voter/Tax Disparities | Census Surnames + Location | BIRDIE (Bayesian Model) | Standard surname (BISG) weighting massively underestimates racial gaps (e.g., calculated 16% gap vs. true 54% gap). | Used surnames as an Instrumental Variable conditioned on outcomes to update probabilities. |
| Fair Lending (PPP) | Loan Apps + LinkedIn | DeepFace vs. BISG | BISG (names) had low correlation (0.54) for Black applicants; produced more errors than correct ID. | Switched to image analysis (0.87 correlation); used specific name filters to remove South Asians. |
| Children's Books | Illustrations | AutoML Vision + Custom CNN | Standard models trained on photos fail to recognize or classify drawn/illustrated faces. | Created custom training set (Illus Face 1.0) of 5,403 hand-labeled illustrations. |
| Job Market (LinkedOut) | LinkedIn Profiles | Predictrace (Names) + StyleGAN2 | Found DeepFace "less reliable than anticipated" for profile pictures in their specific sample. | Abandoned image inference for name-based inference; used human validation for AI-generated faces. |
| Corporate Workforce | LinkedIn Profiles | DeepFace (Images) + rethnicity (Names) | Images are reliable for Black ID but fail for Hispanic ID. Names are reliable for Hispanic ID but fail for Black ID. | Hybrid hierarchy: use pictures for Black ID, but switch to names for Hispanic ID (and vice versa based on confidence scores). |
| Sharing Economy (Airbnb) | Profile Photos | Face++ + Humans (MTurk) | Automated tools often yielded low confidence scores or failed on ambiguous photos. | "Tie-breaker" human review: used 3 humans per photo; manual override by authors if humans disagreed. |
