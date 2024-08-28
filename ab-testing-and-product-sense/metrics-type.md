# Metrics Type

### Binomial Metrics

Binomial metrics, such as whether a user liked a post or not (yes/no), typically require the smallest sample size to achieve significance. This is because:

* They have only two possible outcomes
* Each observation is independent
* The probability of success remains constant for each trial

These characteristics make binomial metrics relatively stable and easier to analyze statistically.

### Percentage Metrics

Percentage metrics, like like rate, are closely related to binomial metrics and often require similar sample sizes. They represent the proportion of users who performed a specific action.

### User-Level Metrics

User-level metrics, such as likes per user, generally require larger sample sizes than binomial or percentage metrics. This is because:

* They can have a wider range of possible values
* They often have higher variance between users
* They may be affected by outliers (e.g., power users who like many posts)

### Sum Metrics

Sum metrics, like total likes, typically require the largest sample sizes to achieve significance. This is because:

* They can have extreme variability
* They are highly sensitive to outliers
* They don't account for the number of users or opportunities for the action to occur

