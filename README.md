<script type="text/x-mathjax-config">
  MathJax.Hub.Config({tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']],displayMath: [["$$","$$"],["\[","\]"]],
        processEscapes: true}});
</script>
<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
#<center>Human Augmented Training Data in Learning To Rank</center>
##<center>Abstract</center>

---
Learning to rank methods and their applications such as relative attributes are extensively used in AI. Though there is a variety of learning to rank methods, those which use pairwise guidance are especially popular. However, pairwise learning to rank methods require a substantial amount of training for the learning algo- rithms to perform well. In this paper we explore how human local annotation can be used to augment existing training beyond the given training pairs. Our empirical results show the promise of such a direction is substantial.
##<center>Introduction</center>

---
Existing pairwise learning to rank rely on high quality training pairs and substantial training samples. It has been widely shown and observed pragmatically in many fields that the more training data available the more useful the ranking function. Conversely, if inconsistent training data is provided performance can be greatly impacted. Our core idea is to use human feedback in the form of localized annotations to generate more training data.The form of feedback is for the human expert to outline what parts of the object are the reason for the ranking. 
<center><div>
    <img src='./img/approach.pdf' width=700px alt="sometext" />
    <p>(a) Given training images A and B, ordered in strength as $A>B$ for attribute "eyes open", (b) we ask a human to annotate the regions most relevant to the attribute. We then synthesize new images $A^{'}_{B}$ and $B^{'}_{A}$ by swapping and blending in the annotated regions.(c-d) The synthesized images can be used to generate new augmented constraints.</p>
</div></center>

---
##<center>Approach</center>
Step 1:Get users' judgement in two images regarding attribute strength.

Step 2:Generate augmented training data.

Step 3:Rank images via RankSVM with original training data and augmented data.
###Augmenting constraints in RankSVM
We now have a way to augment each original ordered training pair $(i,j)$ with synthetic ordered pairs  $(i,i^\prime)$ and  $(j^\prime,j)$.  This set of synthetic ordered pairs $O'=\{(i,i')\}$ creates a new partial-order ranking constraint in the RankSVM formulation:
\[
\begin{align}
\min ~~\frac{1}{2}\|\|w\|\|^2+C(\Sigma\zeta\_{ij}+\Sigma\gamma\_{ij}+\Sigma\eta\_{ij}) \\\\ 
s.t. w^T(x\_i-x\_j)\geq1-\zeta\_{ij}; \forall(i,j)\in O \\\\
\*w^T(x\_i-x\_{i'})\geq \mu(1-\eta\_{ii'}); \forall(i,i')\in O^\prime \\\\
|{w^T(x\_i-x\_j)}|\leq\gamma\_{ij}; \forall(i,j)\in S \\\\
\zeta\_{ij}\geq0; \eta\_{ii'}\geq0; \gamma\_{ij}\geq0,
\end{align}
\]
* are the constraints generated from the synthetic pairs that enforce a new margin $\mu \ge 0$, which is parameter that reflects our confidence in the synthetic augmented data.  When $\mu=1$, the augmented training pairs are weighted equally as the original data pairs, and when $\mu<1$ or $\mu>1$, we give them less or more weight compared to the original pairs, respectively.  In practice, we set $\mu$ via cross validation.

---
##<center>Experimental Result</center>
###Datasets
* LFW10, which is a subset of the Labeled Faces in the Wild dataset. It consists of 2000 human face images (1000 for training and 1000 for testing) with different poses, lighting conditions, and background clutter.
* Shoes-with-attributes (SWA), which is downloaded from like.com and consists of 14658 shoe images;

###Ranking accuracy with augmented training data
We perform detailed analyses using CNN features(Convolutionary Neural Network) and compare only to the stronger original-only baseline due to its superiority over other features.
<center><div>
<img src='./img/vary_face.pdf' width=300px alt="sometext" />
<img src='./img/vary_shoes.pdf' width=300px alt="sometext" />
<p>Ranking accuracy comparing ours (CNN+Augmented) with the baseline (CNN+Original) for varying increments of augmented training data averaged over all attributes on (a) LFW10 and (b) Shoes-with-attributes (SWA).</p>
</div></center>
###Robustness of adding augmented training data
<center><div>
<img src='./img/consistency_face.pdf' width=300px alt="sometext" />
<img src='./img/consistency_shoes.pdf' width=300px alt="sometext" />
<p>We vary the portion of original training pairs from 10% to 100% in increments of 10%, and randomly choose the training pairs in each trial.With our augmented data, the ranker consistently performs better than that using only the original training data across all number of original training pairs.</p>
</div></center>
---
##<center>Claim</center>
Given human's rantionale, we demonstrate one way to teach machine intuitively. We plan to add more dataset to prove the correctness of idea:human augmented training data. And we would like to understand why this phenomenon exists.

