# xgboost

#### 1. Introduction

* **Core Idea:** XGBoost is a scalable tree boosting system designed for efficiency and performance. It incorporates innovations like sparsity-aware algorithms and a weighted quantile sketch for handling large-scale data.

#### 2. Tree Boosting in a Nutshell

*   **Regularized Learning Objective:**



    ```latex
    L(\phi) = \sum_{i} l(\hat{y}_i, y_i) + \sum_{k} \Omega(f_k)    
    ```
* L(ϕ)=∑il(y^i,yi)+∑kΩ(fk)L(\phi) = \sum\_{i} l(\hat{y}\_i, y\_i) + \sum\_{k} \Omega(f\_k)L(ϕ)=i∑​l(y^​i​,yi​)+k∑​Ω(fk​) Ω(f)=γT+12λ∥w∥2\Omega(f) = \gamma T + \frac{1}{2} \lambda \\|w\\|^2Ω(f)=γT+21​λ∥w∥2
  * **Explanation:** This regularized objective helps in preventing overfitting by penalizing the complexity of the model.
*   **Gradient Tree Boosting:**

    L(t)≈∑i=1n\[l(yi,y^i(t−1))+gift(xi)+12hift2(xi)]+Ω(ft)L^{(t)} \approx \sum\_{i=1}^{n} \left\[ l(y\_i, \hat{y}\_i^{(t-1)}) + g\_i f\_t(x\_i) + \frac{1}{2} h\_i f\_t^2(x\_i) \right] + \Omega(f\_t)L(t)≈i=1∑n​\[l(yi​,y^​i(t−1)​)+gi​ft​(xi​)+21​hi​ft2​(xi​)]+Ω(ft​)

    * **Explanation:** The model is trained in an additive manner using a second-order approximation of the loss function.

#### 3. Split Finding Algorithms

*   **Exact Greedy Algorithm:**

    * Used for finding the best split by enumerating over all possible splits.

    Lsplit=12\[(∑i∈ILgi)2∑i∈ILhi+λ+(∑i∈IRgi)2∑i∈IRhi+λ−(∑i∈Igi)2∑i∈Ihi+λ]−γL\_{\text{split\}} = \frac{1}{2} \left\[\frac{(\sum\_{i \in I\_L} g\_i)^2}{\sum\_{i \in I\_L} h\_i + \lambda} + \frac{(\sum\_{i \in I\_R} g\_i)^2}{\sum\_{i \in I\_R} h\_i + \lambda} - \frac{(\sum\_{i \in I} g\_i)^2}{\sum\_{i \in I} h\_i + \lambda} \right] - \gammaLsplit​=21​\[∑i∈IL​​hi​+λ(∑i∈IL​​gi​)2​+∑i∈IR​​hi​+λ(∑i∈IR​​gi​)2​−∑i∈I​hi​+λ(∑i∈I​gi​)2​]−γ
* **Approximate Algorithm:**
  * Proposes candidate splits based on feature percentiles to handle large datasets.
  * **Weighted Quantile Sketch:** rk(z)=1∑(x,h)∈Dkh∑(x,h)∈Dk,x\<zhr\_k(z) = \frac{1}{\sum\_{(x, h) \in D\_k} h} \sum\_{(x, h) \in D\_k, x < z} hrk​(z)=∑(x,h)∈Dk​​h1​(x,h)∈Dk​,x\<z∑​h

#### 4. System Design

* **Column Block for Parallel Learning:**
  * Data is stored in compressed columnar format, sorted by feature values, to reduce computation costs in split finding.
* **Cache-aware Access:**
  * Introduces cache-aware prefetching to mitigate the performance impact of cache misses during split finding.
* **Blocks for Out-of-core Computation:**
  * Data is split into blocks that are stored on disk and processed in memory-efficient chunks.

#### 5. Related Works

* **Comparison with other systems:**
  * XGBoost is compared against other tree boosting systems, highlighting its advantages in scalability and efficiency.

#### 6. End-to-End Evaluations

* **Classification and Ranking Experiments:**
  * XGBoost is tested on various datasets, showing significant speedups and competitive accuracy compared to other methods.
* **Out-of-core and Distributed Experiments:**
  * The system's ability to handle large datasets beyond the capacity of memory is demonstrated through out-of-core and distributed computations.
