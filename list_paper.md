# **Minimum Viable Signal: Venture Funding, Social Movements, and Race**

### **1. Data Source**

- **Primary Database:** The authors use **PitchBook** to identify startups and investors active between 2000 and 2023.
- **Image Collection:** They mainly use **LinkedIn** to scrape profile pictures and biographical information.
- **Supplementary Sources:** For founders without LinkedIn images, research assistants searched **Crunchbase, Twitter, Facebook**, startup websites, and news articles.

### **2. Algorithms**

The authors employed two specific algorithms to assist in classification:

- **DeepFace:** A Python package (Serengil and Ozpinar 2020) that classifies images based on 2,622 machine-determined features.
- **Name Embeddings:** The algorithm by **Ye et al. (2017)** was used specifically to distinguish between Black founders and dark-skinned Asian founders (e.g., South Asians), a common source of false positives in skin-tone analysis.

### **3. Inference Behavior (Rate & Errors)**

- **False Positives:** The authors note that skin-tone classification alone often produces false positives. For example, South Asian founders were frequently misclassified as Black due to darker skin tones.
- **False Negatives:** The algorithms sometimes misclassified light-skinned Black founders as white.
- **Correction:** Because of these error rates, the authors did not rely solely on the algorithmic output and used it primarily as a first pass before manual verification.

### **4. Special Steps**

To ensure high data quality, the authors implemented several rigorous manual steps:

- **100% Manual Review:** The authors manually reviewed **every output** of the image classifier (over 150,000 photos) rather than relying on the algorithm's raw predictions.
- **Contextual Clues:** For ambiguous photos, they examined LinkedIn profiles for "second signals" such as attendance at **Historically Black Colleges and Universities (HBCUs)**, membership in affinity groups (e.g., Black student unions), or languages spoken.
- **"At Least One" Rule:** A startup was classified as "Black-founded" if **at least one** founder was identified as Black. It was only classified as "white-founded" if **all** founders were white.

# Estimating Racial Disparities When Race is Not Observed

### 1. Data Source

The paper uses two main types of data sources: one for inferring race and others for applying/validating the method.

- **For Inference (Inputs):** The method relies on **Census Bureau data**, specifically the surname lists (which provide the racial distribution of surnames) and decennial Census or American Community Survey (ACS) data (which provide the racial distribution of geographic areas like blocks or ZIP codes).
- **For Validation:** They use the **North Carolina voter file**, which contains self-reported race, names, and addresses, allowing them to compare their estimates against a "ground truth".
- **For Application:** They apply the method to **IRS administrative tax data** (Form 1040s), which lacks racial labels, to estimate disparities in the Home Mortgage Interest Deduction (HMID).

### 2. Algorithm

The paper introduces **BIRDIE** (Bayesian Instrumental Regression for Disparity Estimation), which works in two stages:

- **First Stage (BISG):** It starts with Bayesian Improved Surname Geocoding (BISG) probabilities, which calculate the likelihood of a person belonging to a racial group based on their surname and residence location.
- **Second Stage (BIRDIE):** It uses an **Expectation-Maximization (EM) algorithm** to estimate the relationship between race and the outcome.
- **Core Assumption:** The algorithm treats surnames as an **instrumental variable**. It assumes that while race affects the outcome (e.g., political party or tax deduction), the surname itself does not directly affect the outcome after controlling for race and location.

### 3. Rate (Inference Behavior)

The method significantly reduces the error (bias) found in standard estimation methods.

- **Error Reduction:** In the North Carolina validation study, BIRDIE reduced bias by approximately **84%** to **85%** compared to standard BISG weighting methods.
- **Specific Numbers (Voter Party Registration):**
    - **True Gap:** The actual gap in Democratic registration between White and Black voters was **54.6 percentage points**.
    - **Standard Method:** The standard BISG weighting method estimated this gap at only **16.8 percentage points** (a massive underestimation).
    - **BIRDIE Estimate:** The BIRDIE model estimated the gap at **48.8 percentage points**, much closer to the truth.
- **Tax Data Results:** When applied to IRS data, the method found significant disparities in HMID claims:
    - **White filers:** **10.6%** claim the deduction.
    - **Black filers:** **6.5%** claim the deduction.
    - **Hispanic filers:** **4.6%** claim the deduction.

### 4. Steps

The paper describes a specific workflow for applying this methodology:

- **Step 1: Calculate BISG Probabilities:** Compute the initial probability of race for each individual using Census surname and location data.
- **Step 2: Apply BIRDIE Model:** Use the EM algorithm to combine these probabilities with the outcome variable. This "updates" the race probabilities by conditioning them on the outcome information.
- **Step 3: Diagnostic Check (Optional):** The authors describe a diagnostic step using "surname summary statistics" (e.g., grouping surnames by country of origin like "Japanese" or "Irish") to check if the assumption that surnames are independent of the outcome holds true.
- **Step 4: Conditional Estimation (Optional):** For deeper analysis, the method can estimate disparities while controlling for other variables. For example, in the tax application, they estimated HMID claims conditional on homeownership status to see if the racial gap was purely due to differences in owning a home (it was for Black filers, but not for Hispanic filers).

# Regulatory Arbitrage or Random Errors? Implications of Race Prediction Algorithms in Fair Lending Analysis give me something like

### **1. Data Source**

**For Algorithmic Inference (BISG):** The authors use **surnames** and **geographical location** (addresses) as inputs1. The surname data comes from **Census Bureau** lists (e.g., 2010 Decennial Census)2, and location data is matched to Census block or ZIP code demographics.

**For Image Inference:** They scraped **LinkedIn** profiles to obtain images of firm owners, matching them to loan applications using the company name and employment dates.

**For Ground Truth/Validation:** They used **self-identified race** data from **Paycheck Protection Program (PPP)** loan applications to benchmark the accuracy of their proxies.

### **2. Algorithm**

The paper utilizes and compares two primary algorithmic approaches:

**BISG (Bayesian Improved Surname Geocoding):** They employed the standard **Surgeo** Python library to calculate the probability of an individual belonging to a specific racial group based on their surname and location.

- **Image-Based Classifier:**
    
    **Feature Extraction:** They used the **DeepFace** Python package (specifically the **VGG-Face** model) to extract "facial embeddings" (features) from the images.
    
    **Classification:** They trained a **Random Forest** model on these embeddings to classify individuals as Black or non-Black7. The model was trained on a separate dataset of ~170,000 venture-backed startup founders.
    

### **3. Rate (Inference Behavior)**

The authors found significant differences in accuracy between the methods:

**BISG Accuracy:** It performed poorly for Black applicants, with a correlation of only **0.54** with self-identified race9. The algorithm produced more errors (false positives + false negatives) than correct identifications (true positives) for Black borrowers10101010. It underestimated racial disparities in loan approval rates by **43%**.

**Image-Based Accuracy:** This method was much more accurate, achieving a correlation of **0.87** with self-identified race12. The Random Forest model itself achieved **91% accuracy** on a hold-out test sample.

### **4. Special Steps**

The authors implemented several specific steps to refine their data and reduce errors:

**Manual Verification:** They did not rely solely on the image algorithm; they conducted **clerical reviews** (manual checks) of the images, using LinkedIn profile information to verify race when the image was ambiguous.

**South Asian Filter:** They applied a name classifier to specifically **screen out South Asian names**, as the facial recognition software frequently misclassified South Asian individuals as Black.

**Strict Matching Criteria:** To ensure they had the correct person, they required that the company name on the loan application matched an entry on the LinkedIn profile and that the employment start date preceded the loan application.

**Re-sampling for Training:** Because Black founders were underrepresented in their training data (only ~3%), they **sampled with replacement** (oversampled) images of Black founders to create a balanced training set.

# AI Personality Extraction from Faces: Labor Market Implications

### **1. Data Source**

- **Primary Input:** The authors inferred personality traits from **LinkedIn profile pictures** of approximately **96,000 MBA graduates**.
- **Training Data (for the algorithm):** The underlying algorithm (KODSN) was trained on a separate dataset of **12,447 volunteers** who provided self-reported Big 5 personality surveys along with their facial photographs.
- **Validation Data:** To verify stability, they also collected **official MBA photo directories** (student portraits) to compare against the LinkedIn photos of the same individuals.

### **2. Algorithm**

The paper uses a two-stage **Artificial Neural Network (ANN)** approach, specifically an updated version of the model developed by **Kachur et al. (2020)**, referred to as **KODSN**:

- **Stage 1 (Computer Vision):** A deep computer vision network processes the image to create a **128-dimensional face embedding**. This network is pretrained to recognize identity (ensuring the features capture stable facial structure rather than just transient expressions).
- **Stage 2 (Prediction):** A nonlinear prediction model (Multi-Layer Perceptron) maps these 128-dimensional facial features to the **Big 5 personality traits** (Openness, Conscientiousness, Extraversion, Agreeableness, and Neuroticism).
- **Auxiliary Algorithms:** They also used **DeepFace** (VGG-Face) to infer race and facial expressions, and other specific tools to estimate attractiveness, age, and "photoshop" probability.

### **3. Rate (Inference Behavior)**

- **Accuracy:** The correlation between the algorithm's predictions and self-reported personality scores ranges between **0.14 and 0.36** (depending on the trait). The authors note this is comparable to the accuracy of a coworker predicting a colleague's personality (which typically correlates 0.30–0.41 with self-reports).
- **Stability:** When applied to two different photos of the same person (LinkedIn vs. MBA directory), the inferred personality scores had a high correlation of **0.70 to 0.72**, suggesting the model detects stable features rather than random noise.
- **Expression Bias:** The authors found that facial expressions do bias the inference (e.g., smiling makes a person score higher on Agreeableness and lower on Neuroticism), though they control for this in their analysis.

### **4. Steps**

The paper describes a specific pipeline for processing the images:

- **Preprocessing:** Images undergo face and eye detection, alignment, cropping to the face area, conversion to grayscale, and resizing.
- **Filtering:** They filter out images where the face is not correctly positioned, the resolution is too low, or multiple faces are present.
- **Embedding Extraction:** The preprocessed image is fed into the Stage 1 network to get the numerical vector (embedding).
- **Score Generation:** The embedding is fed into the Stage 2 network to output the five personality scores.
- **Control Checks:** They explicitly run additional steps to detect if a photo was **Photoshopped** (using a specific image manipulation detection tool), if the person is wearing **glasses**, and if the image is **blurry**, using these as controls in their regression models.

# What We Teach About Race and Gender: Representation in Images and Text of Children’s Books

### **1. Data Source**

**Primary Input:** The authors digitized and analyzed **1,130 award-winning children's books** published between 1923 and 2019. These were divided into "Mainstream" (Newbery and Caldecott medalists) and "Diversity" collections.

- **Training Data (for Algorithms):**
    
    **For Face Detection:** They created a custom dataset called **Illus Face 1.0**, consisting of **5,403 manually labeled illustrated faces** from their book sample, to train the model to recognize drawings rather than just photos.
    
    **For Classification:** They used the **UTKFace** public dataset (over 20,000 photos manually labeled with race, gender, and age) to train their classification model.
    
    **For Text:** They used **Pantheon 2.0** (Wikipedia biographies) to identify famous figures and **Social Security Administration** data to infer gender from first names.
    

### **2. Algorithms**

The paper employs a "multi-modal" approach using both Computer Vision and Natural Language Processing (NLP):

**Face Detection:** They used **Google’s AutoML Vision** to train a custom transfer learning model specifically for detecting faces in illustrations.

**Skin Color Analysis:** They used a **Fully Connected Convolutional Neural Network (FC-CNN)** combined with a **Conditional Random Field (CRF)** to segment skin areas. They then applied **k-means clustering** ($k=5$) to determine the dominant skin color

**Identity Classification:** They trained another **AutoML Vision** model to classify race (Asian, Black, Latinx, White), gender (Female, Male), and age (Child, Adult).

**Text Analysis:** They used **Named Entity Recognition (NER)** to extract names and specific "token counting" algorithms to quantify gendered pronouns and terms.

### **3. Rate (Inference Behavior)**

**Face Detection Accuracy:** Their custom model achieved **93.4% precision** and **76.8% recall** on their testing data.

**Classification Accuracy:** The demographic classification model achieved **90.6% precision** and **89.0% recall**.

**Limitations:** The authors note that because the classification model was trained on photographs (UTKFace), it is likely less accurate when applied to illustrations. They also highlight that their gender classification is binary (female/male) and based on presentation, which limits its ability to capture non-binary identities.

### **4. Special Steps**

The authors describe several specialized steps to handle the unique challenge of illustrated children's books:

- **Skin Color Conversion:** To measure skin tone accurately, they converted RGB values to the *CIELAB ($Lab$) color space*, which is "perceptually linear" (meaning numerical changes correspond to human perception of lightness/darkness). They then calculated a "perceptual tint" score (0–100).
- **"Seen vs. Heard" Comparison:** They explicitly compared the ratio of female faces in images ("seen") to female pronouns/names in text ("heard"), finding that females are more likely to be pictured than they are to speak or be mentioned in the text.
- **Economic & Social Correlation:** They linked their representation data to **library checkout logs** (Seattle Public Library) and **consumer purchase panels** (Numerator OmniPanel) to measure how the race/gender of a book character correlates with who buys or borrows the book

# **LinkedOut? A Field Experiment on Discrimination in Job Network Platforms**

### **1. Data Source**

- **For Targets (Real Users):** The authors inferred the race of the people they contacted (the "targets") primarily using **names**. They utilized data from the U.S. Census Bureau (for last names) and the **Social Security Administration** (for first names).
- **For Profiles (Fictitious Users):** For the experimental profiles they created, they did not *infer* race but *generated* it. They used a dataset of 100,000 images from **StyleGAN2** as the source material to create synthetic "Black" and "White" faces.

### **2. Algorithm**

**For Inference (Targets):** They used the **`predictrace`** package in R to predict race based on the targets' first and last names3333. They explicitly noted that they initially attempted to use **DeepFace** to infer race from profile pictures but found it "less reliable than anticipated," so they reverted to name-based inference.

**For Generation (Profiles):** They developed a custom transformation algorithm using **StyleGAN2**. They calculated the average "vector difference" between Black and White faces and added/subtracted this vector to synthetic images to change the race while keeping other facial features (like expression and background) stable.

### **3. Rate (Inference Behavior)**

**Target Demographics:** Using the name-based algorithm, they classified the population of LinkedIn users they contacted as **69% White**, **13% Hispanic**, **10% Asian**, and **6% Black**.

**Profile Validation:** To ensure their generated profiles were perceived correctly, they ran a validation study ($n=506$). Participants correctly identified the race of the AI pictures and rated Black and White profiles as highly comparable in trustworthiness and intelligence7. The AI images were detected as "fake" only **12–14%** of the time, which was statistically indistinguishable from real photos (15%).

### **4. Special Steps**

The authors describe several unique steps to handle race in this experiment:

- **Picture Swapping:** Halfway through the experiment (Stage II), they **swapped the profile pictures** of their fictitious accounts. A profile that was previously "Black" became "White" (and vice versa) to test whether discrimination persisted when the network composition was held constant.
- **Twin Pairs:** They created "twin" profiles that had identical CVs and names but differed *only* in the race of the AI-generated profile picture. This allowed them to isolate the effect of race from other variables.
- **Manual Filtering:** For the AI-generated faces, they manually removed images with artifacts like earrings or "weird deformations" to ensure they looked realistic.

# **The Missing Link? Using LinkedIn Data to Measure Race, Ethnic, and Gender Composition of Company Workforces**

### **1. Data Source**

**Primary Inputs:** The authors inferred race and ethnicity using **LinkedIn profile pictures** and **names**.

**Data Extraction:** They used a third-party service called **Proxycurl** to scrape publicly available LinkedIn data, including employment history, education, and profile photos.

**Sample:** They extracted 112,280 worker profiles across seven specific companies (e.g., Meta, SpaceX, GSK).

### **2. Algorithm**

They used a "multi-modal" approach combining two different algorithmic tools:

**For Images (Picture Classification):** They used the **DeepFace** package in Python4. This model was pre-trained on the **FairFace** dataset to classify race/ethnicity.

**For Names (Name Classification):** They used the **`rethnicity`** package in R, which predicts ethnicity based on names using statistical data.

### **3. Rate (Inference Behavior)**

**Completeness:** They were unable to classify race/ethnicity for about **4.55%** of names (e.g., if only an initial was used)7. About **23%** of profiles were missing a picture.

- **Reliability by Group:**
    - **Black Classification:** The authors found that **pictures** were far more reliable than names for identifying Black individuals. The probability distribution for pictures was "bimodal" (clear spikes at 0% or 100%), whereas names were less definitive.
    - **Hispanic Classification:** Conversely, they found that **names** were more reliable than pictures for identifying Hispanic individuals. The image classifiers often struggled to distinguish Hispanic individuals from other groups due to skin tone overlaps.
    
- **Validation:** When compared to aggregate company diversity reports (e.g., Meta's EEO-1 report), their estimates for the percentage of Black and Hispanic employees were very close (e.g., LinkedIn est. **6.6%** Black vs. Company Report **4.4-4.9%** Black).

### **4. Special Steps**

The authors created a specific hierarchy of rules to combine the two algorithms, rather than just averaging them.

- **For Black Classification (Picture First):**
    - Primary: Use the **picture** prediction.
    - Secondary: If the picture is missing, use the **name**.
    - Override Rule: If the picture prediction is uncertain (probability < 50%) but the name prediction is highly certain (probability > 90%), they switch to using the name.
- **For Hispanic Classification (Name First):**
    - Primary: Use the **name** prediction.
    - Secondary: If the name is missing, use the **picture**.
    - Override Rule: If the name prediction is uncertain (probability < 50%) but the picture prediction is highly certain (probability > 90%), they switch to using the picture.

# Racial Discrimination in the Sharing Economy: Evidence from a Field Experiment

### **1. Data Source**

- **For Hosts:** The authors scraped **host profile images** from Airbnb listing pages.
- **For Guests (Experimental Accounts):** They did not use images for their own test accounts to avoid confounding factors. Instead, they used **first names** that distinctively signal race.
- **For Past Guests:** They collected **profile pictures** of guests who had previously reviewed the hosts.

### **2. Algorithm**

- **For Hosts:** They used **human classification** via Amazon Mechanical Turk. Workers categorized hosts into racial groups (White, African American, Asian, Hispanic, multiracial, or unknown).
- **For Guests (Experimental Accounts):** They used a **name-based assignment** method. They selected 20 names (10 distinctively African American and 10 distinctively White) derived from Massachusetts birth certificate data (1974–1979) and prior research by Bertrand and Mullainathan.
- 
    
    **For Past Guests:** They used **Face++**, an automated face-detection API, to categorize race, gender, and age.
    

### **3. Rate (Inference Behavior)**

- **Name Validation:** A survey confirmed the names strongly signaled race. On a scale of 0 (African American) to 1 (White):
    - White female names scored above **0.90**.
    - African American female names scored below **0.10**.
    - White male names scored above **0.88**.
    - African American male names scored below **0.10** (with one exception at 0.26).
- **Face++ Confidence:** The automated API provided a confidence score for its predictions. The authors only accepted the API's classification if the confidence was **95 out of 100** or higher.

### **4. Special Steps**

The authors employed several backup measures to ensure accuracy:

- **"Tie-Breaker" Worker:** For host classification, two Mechanical Turk workers reviewed each image. If they disagreed, a **third worker** was hired to settle the dispute.
- **Manual Override:** If all three workers disagreed (e.g., if the profile picture was a sea turtle), the authors **manually coded** the image themselves.
- **Hybrid Approach for Past Guests:** If the Face++ API failed to detect a face or reported low confidence (<95), they reverted to using **Mechanical Turk workers** to classify the image instead.
- **Speed Survey:** To validate the guest names, they enforced a **3-second time limit** for survey respondents to categorize names, ensuring the names triggered a "gut" perception of race rather than careful deliberation.

# The Value of Internal Labor Markets: Evidence from LinkedIn Profiles and U.S. Inventors

### **1. Data Source**

- **Primary Inputs:** The authors used **names** and **profile pictures** from LinkedIn profiles.
- **Validation Data:** They benchmarked their inferred demographic data against U.S. Census Bureau statistics (ACS PUMS) and **EEO-1** (Employer Information Report) administrative records.

### **2. Algorithm**

The authors used a hybrid approach depending on the specific demographic group being inferred:

- **For White and Black Classification:** They analyzed **LinkedIn profile photos** using two Python packages: **DeepFace** and **OpenCV**.
- **For Asian, Hispanic, and Gender Classification:** They inferred these characteristics based on **names**.

### **3. Rate (Inference Behavior)**

- **Alignment with Benchmarks:** The authors state that their LinkedIn-based demographic measures "largely track" nationally representative sources like the American Community Survey (ACS).
- **Selection Bias:** They acknowledge that their matched sample (inventors linked to LinkedIn profiles) is skewed toward **more successful** and **male** inventors compared to the full population of U.S. inventors666. For instance, women make up 8.32% of the matched sample versus 10.31% of the full inventor dataset.

### **4. Special Steps**

- **Split Methodology:** They explicitly separated their inference logic:
    - **Names** were used to estimate the likelihood of a user being a **Woman, Asian, or Hispanic**.
    - **Photos** were used specifically to infer the likelihood of being **White or Black**.
- **Reference to External Methodology:** The detailed procedures for this inference are documented in a separate paper by the authors (Wang, Shin, and Zhang 2025).

# Organizational Diversity Database: An Open Dataset on Firm Racial and Gender Composition

### **1. Data Source**

- **Primary Input:** The authors collected **570 million publicly available LinkedIn profiles**.
- **Specific Features:** They used the **names** and **profile pictures** associated with these LinkedIn users to perform the inference.

### **2. Algorithm**

The authors employed a hybrid approach depending on the specific demographic group:

- **For Black and White Classification:** They analyzed LinkedIn profile **photos** using two specific Python packages: **DeepFace** and **OpenCV**.
- **For Asian and Hispanic Classification:** They inferred these racial identities based on **names**, estimating the likelihood of a user being Asian or Hispanic.
- **For Gender:** They also used **names** to estimate the likelihood of a user being a woman.

### **3. Rate (Inference Behavior)**

- **Validation:** The authors validated their inferred measures by comparing them against administrative data from the **EEOC** (Equal Employment Opportunity Commission). The paper includes graphs showing the association between their LinkedIn-based measures and the actual proportion of workers from different demographic groups in the EEOC data.
- **External Correlation:** They also checked their measure of Black workforce proportion against "Black-White callback gaps" from prior research (Kline et al., 2024), demonstrating a relationship between their diversity data and real-world discrimination metrics.

### **4. Special Steps**

- **Aggregation:** Unlike many studies that look at individuals, this paper aggregates the inferred data to the **firm-occupation-year level** to create a dataset on organizational diversity.
- **Coverage Threshold:** To ensuring the data was representative, they applied an **"EEO score threshold" of 0.3**. This means they only included firms in their analysis if at least **30%** of the firm's total workforce was represented on LinkedIn.
- **Reference to Prior Work:** The authors note that the detailed technical procedures for the name and photo inference are documented in a related paper (Wang, Shin, and Zhang 2025).
