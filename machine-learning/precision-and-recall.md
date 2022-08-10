# Precision and Recall

## Explaining precision and recall <a href="#d80f" id="d80f"></a>

The first days and weeks of getting into NLP, I had a hard time grasping the concepts of precision, recall and F1-score. Accuracy is also a metric which is tied to these, as well as micro-precision and macro-precision. These metrics are important in general machine learning and deep learning as well.

However one of my colleagues (Thanks Marci!) explained it in an excellent way in which I’d thought I’d share. Many of you have probably already seen this, but for me it was a revelation in its simplicity. Credit is also due to “Foundations of Statistical Natural Language Processing” the book where I think this first surfaced.

### Low recall, low precision <a href="#5640" id="5640"></a>

Everything gets better if you use a Venn-chart ? No? Everything within the dotted line represents the system output for “hot dogs” and everything within the full line represents the ground truth “hot dogs” (actual images of “hot dogs”). Everything inside the outer square represents the full set of images.

![](https://miro.medium.com/max/1400/1\*u8PgZ\_84no\_swpLnuMf-PQ.png)

Let’s explain the meaning of “True negatives”, “False negatives”, “True positives” and “False positives”

* True negatives (denoted tn) samples in your data, which you classified as not belonging to your class correctly. Eg. your “hot dog” vs “not hot dog” image classifier correctly classified your image of a car as not being a “hot dog”.
* False negatives (denoted fn) samples in your data, which you classified as not belonging to your class, incorrectly. Eg. your “hot dog” vs “not hot dog” image classifier incorrectly classified an image of a messed up “hot dog” as not being a “hot dog”.
* True positives (denoted tp) samples in your data, which you classifed as belonging to your class correctly. Eg. your “hot dog” vs “not hot dog” classifier correctly classifies a “hot dog” as being a “hot dog”.
* False positives (denoted fp) samples in your data, which you classified as belonging to your class incorrectly. Eg. your “hot dog” vs “not hot dog” classifier incorrectly classifies a hamburger as being a “hot dog”.

How would the charts look in other scenarios?

### High recall, low precision <a href="#1c26" id="1c26"></a>

![](https://miro.medium.com/max/1400/1\*ElFfGqp10lKK\_sY5qrj1Tg.png)

High recall, low precision. Our classifier casts a very wide net, catches a lot of fish, but also a lot of other things.

Our classifier thinks a lot of things are “hot dogs”; legs on beaches, fries and whatnot. However it also thinks a lot of “hot dogs” are “hot dogs”. So from our set of images we got a lot of images classified as “hot dogs”, many of them was in the set of actual “hot dogs”, however a lot of them were also “not hot dogs”.

### Low recall, high precision <a href="#9032" id="9032"></a>

![](https://miro.medium.com/max/1400/1\*gr8KCdTa0NftwE3N6l7i8g.png)

Low recall, high precision. Our classifier casts a very small but highly specialized net, does not catch a lot of fish, but there is almost only fish in the net.

Our classifier is very picky, and does not think many things are hot dogs. All the images it thinks are “hot dogs”, are really “hot dogs”. However it also misses a lot of actual “hot dogs”, because it is so very picky. We have low recall, but a very high precision.

### High recall, high precision <a href="#b42c" id="b42c"></a>

![](https://miro.medium.com/max/1400/1\*mWXZtLkXDPdojIivZPY0Hg.png)

The holy grail, our fish net is wide and highly specialized. We catch a lot of fish (almost all of it) and we almost get only fish, nothing else.

Our classifier is very good, it is very picky, but still it gets almost all of the images of “hot dogs” which are “hot dogs” correct. We are happy!

## Reference

{% embed url="https://medium.com/@klintcho/explaining-precision-and-recall-c770eb9c69e9" %}
