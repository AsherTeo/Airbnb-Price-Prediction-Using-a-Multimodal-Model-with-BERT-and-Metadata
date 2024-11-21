# Overview

This project focuses on developing a robust Multi-Modal Machine Learning model to predict Airbnb prices in Melbourne. The dataset comprises 275 diverse features, including property details, location attributes, and host information. The process began with Exploratory Data Analysis (EDA) to identify trends, correlations, and patterns, followed by Data Transformation, where comprehensive feature engineering, scaling, and encoding were applied to prepare the data.

To incorporate textual features like property descriptions, BERT embeddings with max pooling were employed to extract meaningful contextual representations, which were seamlessly integrated with numerical and categorical features to enhance model performance. Machine Learning models, such as XGBoost, were trained and fine-tuned using Optuna to achieve optimal results. The model's performance, evaluated using Mean Absolute Error (MAE) and Root Mean Squared Error (RMSE) on a testing dataset, demonstrated competitive results that outperformed benchmark scores from the author’s [GitHub repository](https://github.com/georgian-io/Multimodal-Toolkit). 

# Dataset

The Airbnb Prices Dataset can be found [here](https://github.com/georgian-io/Multimodal-Toolkit/tree/master/datasets/Melbourne_Airbnb_Open_Data). The dataset consists of 18,297 training samples, 2,288 validation samples, and 2,288 test samples.

# Exploratort Data Analysis(EDA)

This section presents a straightforward Exploratory Data Analysis (EDA) to explore features related to pricing. The following visualizations offer valuable insights into the data:

**Example of Numerical Features**

- **Left Image: The price range does not increase consistently with the number of bedrooms**
- **Right Image: There appears to be no clear correlation between the number of rooms and price, as properties with 2 rooms can have prices comparable to those with 8 rooms. This suggests that other factors might play a more significant role in determining price.**
  
  <table>
<tr>
    <td><img src="https://github.com/user-attachments/assets/dc91f5b3-008b-4d82-a30c-46ab94778ec4" alt="Image 1" width="400"/></td>
    <td><img src="https://github.com/user-attachments/assets/cdcc3fb0-e29a-46d3-9731-e02cc1786293" alt="Image 2" width="400"/></td>
</tr>
</table>

**Example of Categorical Features**

- **Left Image: The type of room appears to significantly influence price, as entire homes are generally more expensive than shared rooms, reflecting their higher value and privacy.**
- **Right Image: Location seems to play a critical role in determining Airbnb prices. For instance, Darebin and Casey exhibit the highest average prices, with listings averaging approximately 13k.**
  
  <table>
<tr>
    <td><img src="https://github.com/user-attachments/assets/4d21c315-07f4-4ec7-ba77-ebf3f13975ac" alt="Image 1" width="400"/></td>
    <td><img src="https://github.com/user-attachments/assets/27d3a76a-af01-4f54-821a-6a392dd30e64" alt="Image 2" width="400"/></td>
</tr>
</table>

# Data Proprcessing and Transformation

## 1) Target Variable (Price):

- Checked minimum and maximum values for outliers.
- Removed prices in the top 5% and bottom 95% of the dataset to eliminate extreme outliers.

## 2) Feature Selection and Transformation:
**Numerical Features:**
- Removed features with variance inflation factors (VIF) > 20 to address multicollinearity.
- Applied Box-Cox transformation to normalize skewed features (skewness > 0.8).
- Retained features with statistical significance (p-value < 0.05).
  
**Categorical Features:**
- Evaluated importance using Chi-Square test; retained the top 70 features based on scores.

## 3) Textual Features:
- Used BERT-base-multilingual-cased to generate embeddings for property descriptions.
- Aggregated embeddings using Max Pooling for better contextual representation.

# Machine Learning

This section outlines experiments conducted using various feature combinations and machine learning methods to predict Airbnb prices. Different data types, such as BERT-base-multilingual-cased embeddings, were evaluated to assess their impact on performance.

The table below summarizes the experiments, detailing the features used, methods applied, and evaluation metrics (Mean Absolute Error - MAE, and Root Mean Squared Error - RMSE):

bert-base-multilingual-cased

| **#** | **Features**       | **Methods**                        | **Test MAE** | **Test RMSE** |
|-------|---------------------|------------------------------------|--------------|---------------|
| 1     | Combined Text Only          | BERT Embedding → Linear                     | **77.353**     | **178.627**      |
| 2    | Meta Only          | 2-Layer MLP                      | **46.577**     | **136.621**      |
| 3     | Meta Only          | Fine-Tuned XGB                    | **44.115**     | **138.052**      |
| 4     | Combined Text + Meta        | BERT Embedding + Meta → Linear MLP          | **47.210**     | **137.606**      |
| 5     | Text + Meta        | TF-IDF + Meta →  Fine-Tuned XGB               | **43.788**     | **138.038**      |
| 6     | Combined Text + Meta        | BERT Embedding + Meta →  Fine-Tuned XGB       | **45.693**     | **140.799**      |
| 7     | Text + Meta        | BERT (MaxPool) + Meta →  Fine-Tuned XGB       | **43.551**     | **136.251**      |
| 8     | Text + Meta        | BERT (MeanPool) + Meta →  Fine-Tuned XGB      | **43.775**     | **136.390**      |
| 9     | Text + Meta        | BERT (MaxPool) + Meta → 2-Layer MLP | **45.852**     | **137.473**      |
| 10     | Text + Meta        | BERT (MeanPool) + Meta → 2-Layer MLP | **46.319**     | **137.098**      |
| 11    | Combined Text + Meta        | BERT (MaxPool) + Meta →  Fine-Tuned XGB       | **44.208**     | **137.736**      |
| 12    | Combined Text + Meta        | BERT (MaxPool) + Meta → 2-Layer MLP | **46.254**     | **140.097**      |
| 13    | Convert all features to text       | BERT Embedding → Linear       | **78.446**     | **176.159**      |

- **Model 1**: Used only text features via a BERT-based model and performed reasonably well.
- **Model 3**: Fine-tuned XGBRegressor on meta features delivered strong results for numerical data alone.
- **Model 5**: TF-IDF with meta features produced competitive results with fewer resources.
- **Model 6 & 7**: Pooling strategies with BERT embeddings demonstrated promising performance when combined with meta data.

---

### Notes
- **BERT Embedding**: Feature vectors extracted from a pre-trained BERT model.
- **MaxPooling/MeanPooling**: Methods to aggregate BERT embeddings into fixed-length vectors.
- **Meta Data**: Includes numerical and categorical information processed through an MLP or concatenated with text features.

