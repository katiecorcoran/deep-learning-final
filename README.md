### Introduction
Understanding and analyzing human movement is a growing area of interest in sports science, physical therapy, and computer vision. In particular, form analysis during exercise offers the potential to improve safety, performance, and personalization of training. Traditionally, such analysis requires full video interpretation, which is computationally expensive and prone to noise. A promising alternative is to rely on pose estimation, which reduces a high-dimensional video down to key joint coordinates.

This approach is especially interesting because it leverages pose data as a lightweight, interpretable alternative to full video, reducing complexity while retaining the essence of human movement. With increasing interest in computer vision for fitness tracking, coaching, and injury prevention, this kind of analysis could eventually power feedback systems in apps, wearables, or automated physical therapy tools.
This project explores whether pose detection data alone can be used to classify common workout movements and uncover variations in form. Specifically, the question I investigate is: **Can we use pose detection to gain insight into people’s form when working out?**

My approach began with a supervised classification task using pose sequences to identify exercises, followed by unsupervised clustering within classes to explore variations in form. While my results were promising, further refinement and evaluation would be needed to confirm the broader effectiveness of pose-based analysis in practical settings.

---

### Approach 
I used a dataset of workout videos sourced from YouTube and curated on Kaggle. These videos were already cleaned and preprocessed to be 10 seconds long, removing background distractions and standardizing length. This made them well-suited for pose-based analysis. Each video was labeled with the performed exercise, allowing for supervised learning. While the dataset does not include ground truth on form quality, it contains enough repetitions across exercises to enable both classification and clustering.

To prepare the data, I extracted joint positions using a pose estimation library. Each frame was transformed into 2D keypoint coordinates (33 joints × 2). I then normalized the sequences and split them into training, validation, and test sets. I initially processed the test dataset provided by Kaggle, but found that it was not preprocessed in the same way my train and validation sets were. Because of this, I pivoted to using 15% of my data designated as validation as test instead. Additional preprocessing included computing elbow angles and creating a parallel set of features focused on arm joints to emphasize critical regions for curl-based movements.

I began with a Random Forest classifier on flattened pose sequences as a simple baseline. It achieved decent performance of around 82% accuracy and highlighted key joint features. Based on the structure of the data, I moved to a 1D Convolutional Neural Network (CNN), which is well-suited for recognizing spatial-temporal patterns in sequential data. The CNN outperformed the Random Forest, achieving around 84% validation accuracy.

I also tested a bidirectional LSTM, which theoretically captures longer-term temporal dependencies. However, it underperformed relative to the CNN and trained more slowly. I then augmented the CNN with additional features: the elbow and surrounding joints were extracted and concatenated with the original pose sequences to emphasize critical movement information. This boosted classification accuracy to approximately 87%. I also tried augmenting the CNN data with elbow angles, but the elbow angle features did not improve accuracy.

---

### Analysis and Results
The final CNN model achieved approximately 88% accuracy on the test set. Misclassifications were primarily between hammer curls and barbell bicep curls, which is intuitive given the similarity in movement. To understand these misclassifications, I inspected feature importance from the Random Forest baseline. Joints 13 and 14 (the elbows) emerged as the most important, validating their relevance. In response, I engineered additional input by including joints 12 through 15 (shoulder to wrist), effectively increasing the weight of these key features. This resulted in a small accuracy boost.

To explore deeper insights, I conducted unsupervised clustering using embeddings from the CNN model. I clustered pose sequences and analyzed the resulting groups within each exercise. Most exercises clustered into a dominant group, but a few fell into multiple clusters, notably planks, which split into two roughly equal clusters. This could represent form variants like bent arms or hip position.

To investigate the plank clusters, I overlaid average joint positions from each cluster, which revealed some posture differences. For instance, in plank clusters, cluster one showed tighter elbow and shoulder configurations, while cluster two showed a more spread out pattern. I watched back videos from different clusters, but I found it difficult to identify the differences. The CNN embeddings could be picking up on patterns and angles that are more imperceptible to humans.

These findings suggest that pose embeddings not only classify exercises but can also capture nuanced style variations, which may be relevant for form evaluation or personalization.

---

### Discussion, Conclusions, and Next Steps
This project demonstrates the potential of pose-based analysis as a tool for understanding workout movement. While I initially hoped to classify "good" vs. "bad" form, the dataset's lack of poor-form examples limited that goal. However, I was still able to explore meaningful movement differences, improve classification accuracy using targeted joint features, and uncover potential subtypes within exercises using clustering and pose overlays. The CNN embeddings revealed posture-level distinctions that were subtle yet detectable, especially in static movements like planks.

The primary limitation is that the dataset only contains “good” form and was not designed for form quality analysis. Still, the ability to improve classification accuracy and detect intra-class variance suggests that the approach has promise. That said, further work would be needed to establish whether pose-based analysis is broadly effective across diverse users, camera angles, and form styles.

Pose detection shows promise for exercise classification and may reveal stylistic differences in how movements are performed. CNNs applied to joint sequences offer a useful modeling approach, and unsupervised analysis can surface interesting patterns. However, further evaluation is needed before strong conclusions can be made about its general effectiveness.

Next steps include collecting a dataset that includes poor or incorrect form to better answer my original question. I’d also like to explore dynamic form patterns by analyzing joint positions over time to count reps or measure execution tempo. Additionally, building a subject-level identifier could reveal whether individuals perform exercises in consistent or unique ways. Finally, contrastive learning approaches could improve the quality of embeddings for clustering and style detection.

---

Kaggle Data Source: https://www.kaggle.com/datasets/philosopher0808/gym-workoutexercises-video



