# JACT Reviewer Response 

**10/06/2023:** This document records the reviews we obtained from a prior submission of the manuscript to the *Journal of Applied and Computational Topology* (JACT) for the submission _Move Schedules: Fast persistence computations in coarse dynamic settings_. 

## Overall changes: 

The paper is largely still the same paper. The main edits we have made: 

- Move algorithms pseudocode moved up from the appendix into the paper
- Scheduling algorithm pseudocode simplified into "online" form, notation has also been simplified 
- All known grammatical errors have been fixed.



## Reviewer 1



## Reviewer 2

> I believe the paper contains very good and interesting results, but it needs a revision before being published.
>
> Pros: I really like the main idea of the paper: using the machinery of moves that allows one to efficiently recompute the R=DV decomposition after one cyclic permutation in the input filtration, the authors find a heuristic to decompose an arbitrary permutation of the filtration into a sequence of moves of provably minimal length and small cost (this is the heuristic part). The authors show experimentally that this idea works. The paper is as self-contained as possible and the authors work out some concrete examples which are helpful to the reader.
>
> Cons: The detailed list of the remarks follows. Sorry, it’s not ordered by importance, so there are trivial things like grammar/typos and more serious issues.
>  In general, I feel that the paper is a bit too verbose - it can be made shorter without losing virtually any content.
> The most important issues that are listed below are: the comment about ‘MoveLeft’ and some missing (or hard to find) details about the algorithm (GreedySchedule in Alg. 1).



Thank for you the constructive review. We believe we've addressed the main concerns of the reviewer in the current revision. We'd remark that verbosity is something we have tried to address, Please see the point-by-point responses for details. 




----

### Point by point responses

> Also, I would appreciate if the authors formulated  exactly what is the implementation they used in their experiments (e.g., did they really use treaps?). 

- In practice, we did not use implicit treaps but rather we  just compute prefix sums (e.g. `np.cumsum`) independently at every step of the scheduling on an array-based implementation (this is the $\mathcal{A}$ data structure in outlined in section 3.4.2). The code that implements the greedy minimization is actually $O(d^2)$, making the complexity of our actual implementation higher that in the paper, but we observed in practice computing prefix sums is incredibly fast due to their implementations being vectorized. 

> Page 1, abstract: I don’t understand the $O(m \log \log m)$ estimate here. According to Thm. 1, this is the time it takes just to find $d$, the length of the optimal schedule. Computing the schedule itself costs $O(dm \log m)$ or $O(m \log m)$ per update. Shouldn’t this be in the abstract?

* Yes, the $O(m \log \log m)$ complexity refers to the complexity of finding the length of the schedule and the LIS (only). We left out the complexity of constructing the schedule itself in the abstract as we envision there are situations where the algorithm used to actually construct the schedule may vary---depending on the particular data structure of choice supporting the operations outlined in 3.3 (e.g. implicit treaps, segment tree, vEB tree), the deterministic and expected time complexity may change.

> Also, why ‘simulation of persistence’ (this expression will appear below, too)?

- We agree the phrase 'simulation of persistence' may be confusing to some, so we've replaced all sentences using the phrase with "computing persistence dynamically." As to why the phrasing was chosen originally, the intuition goes back to vineyards:  one wants to compute persistence along a continuous homotopy $h_t$, for all $t \in [0,1]$, and to do this vineyards is sufficient---but in practice the homotopy must be discretely approximated at every simplex (e.g. using PL, $x$-monotone, or polynomial curves). Thus, we think of the vineyards representation as a process of *simulating* a homotopy. 

> Page 4, 'if the relations are available a-priori' – do the authors mean ‘edges’?

- Yes, changed to edges for clarity.

> Page 7, ‘we summarize our main results (Theorem 1)’. The main claim in theorem 1 is that one can efficiently compute only the length of the optimal schedule. Will the authors consider reorganizing?

- see above. 

>  Page 8: Although $d$ can be  $O(m)$, we provide evidence that $d ~ m - sqrt(m)$. Well, $m - \sqrt(m)  = O(m)$, why ‘although’? Should it be ‘Although d can be O(m) and we provide evidence that d ~ m - sqrt(m), d is much smaller in practice’?

- We aware that $m - \sqrt m  = O(m)$, which is why we omitted the big-O in the notation $d ~ m - \sqrt m$. We amended the ordering the statements to clarify this;  it now states "We provide evidence that $d \sim m - \sqrt{m}$ in expectation for random filtrations...although this implies $d$ can be $O(m)$ for pathological inputs, we give empirical results suggesting $d$  can be much smaller in practice." 

> Page 8: ‘an simplexwise’ -> a simplexwise

- fixed. 

>  Page 9: I am more used to the ‘positive/negative simplex’ terminology than ‘creator/destroyer’.
> This is a matter of personal preference, of course.

- We prefer creator / destroyer. 

>  Page 10: ‘Despite this high algorithmic complexity, the number of column operations has been observed to be super-linear in practice’ – this has to be rephrased, because there is no contradiction (order of m^2 column operations is super-linear, too).

- This statement has been removed. 

> Sec. 2.3, Moves. Sorry, it might be my fault but I had difficulty understanding the procedure from the description. I believe having the pseudocode right here will be helpful (after I read it in the appendix, I was happy with the algorithm). Especially since the algorithm is needed in the proof of Prop. 2, too.

- We previously moved much of pseudocode to the appendix in order to streamline the presentation of the concepts, as the pseudocode for both algorithms takes up an entire page, and the pseudocode for MoveRight is also available in Busaryev's paper. We have moved them back. 


> Page 16, Alg. 1. a) The authors say ‘Algorithm 1 may be easily modified to be completely online, keeping at most two filtrations and one decomposition in memory’. I agree, but I believe it would be equally easy and more natural to state it in this form immediately. The change is in modifying the meaning of \rho_i: while now it is the permutation taking f_1 to f_i, it should be replaced with the permutation taking f_{i-1} to f_i.

- We agree! The algorithm is now presented in the much simpler "online form", which only requires as input a pair of filtrations and a single D = RV decomposition. The original batch-processing presentation of pseudocode reflected more closely the implementation we used (we found it more efficient to compute a batch of schedules, concatenate them, and then give them to the moving algorithm to process sequentially, saving the diagrams between each schedule execution).

>  b) Is it correct that the authors use both MoveRight and MoveLeft in their implementation? I believe it is necessary, and Alg. 1 reflects that; however, it raises some questions about the sequel. I’ll get back to this point.

- Yes! 

> c) Line 6 calls the function GreedySchedule. There is no such algorithm in the paper. There is Alg. 5 in the appendix, LCS-sort, but there is no greedy heuristic in it. Also, it computes the LIS internally, while GreedySchedule takes the longest increasing sequence as a second argument. Please fix this.

- TODO: The GreedySchedule algorithm wasn't included in part because it corresponded to simply modifying line 6 in Alg. 5 (choosing the next 'symbol' / simplex to move) with equation (22). In this revision, we've not only made this much more explicit, but we've also simplified the notation of the inner schedule construction algorithm.  

>  Page 17, the paragraph after Def. 1: ‘the former can be that can be solved optimally’ - this sentence should be fixed.

- Fixed. 

> Page 19: ‘small changes … affects’ -> affect.

- Fixed 

> Note that $m_{i,j}$ is defined here and used in Alg. 5 at the very end of the paper without explanation.

- The usage in Alg. 5 was intentional as it defined in equations (10) and (11); a reference to those equations has been added in Alg. 5. 

> Page 20: ‘allows use to exploit’ -> allows us to exploit

- Fixed. 

> Page 21: Do I understand correctly that this is what is going on in the proof of Corollary 1: $E(m - |LCS(p, q)|) = m - 2 \sqrt(m) - cm^{⅙} + o(m^{⅙})$ by the known result of Baik for $E|LIS(p)|$, therefore it is less than $m - \sqrt(m)$ ? So any function which is asymptotically less than $ 2 \sqrt(m) - cm^{⅙} + o(m^{⅙} $ would do? 

- The expectation result implies that $\mathbb{E}[LIS(p)]$ for random $p \in S_m$ is something slightly smaller than than $2\sqrt m$,  thus we expect the size of the LCS between random permutations $p,q \in S_m$ to be slightly larger than $m - 2\sqrt{m}$, which we simplify by stating "... no larger than $m - \sqrt{m}$."

> Another question : Corollary 1 speaks about two random filtrations, which the authors interpret as ‘two permutations chosen uniformly at random’ (explaining why this is okay in light of Boissonat’s result). But the longest increasing sequence result by Baik is for a single permutation drawn uniformly at random. Is it obvious that, if p and q are drawn from S_m uniformly at random, then $p \circ q^{-1}$ (the permutation we should consider for LIS) is also uniformly distributed? Well, for symmetry reasons it seems to be true and obvious, but, given the level of details in the other parts of the paper, the authors may want to include this as well.

- We agree that $p \circ q^{-1}$ is uniform, and we agree the proof strategy for Corollary 1 outlined by the reviewer would match the level of detail of the rest of the paper. That said, in our struggle to keep the length of the paper manageable we chose to present the proof in a succinct and informal way. 

> Page 22, ‘It is clear from Corollary 1’ -  wrong reference. Probably, from the proof of proposition 3 (Prop. 3 itself does not say anything about LIS).

- Yes, this used to be its own corollary. The proof of prop 3 is indeed the reduction we're referring too. We've amended the statement to explicitly point to the proof.  

>  Let T denote a ordered set-like -> an ordered set-like

- Fixed. 

> Page 27: equation (22) may solved  -> equation (22) can be solved

- Fixed. 

> General comment on the greedy heuristic: does most of the reasoning in section 3.4 apply to the case of MoveRight only? The starting point is the term \sum |I_k| + |J_k|, which only applies to moving simplex i to simplex j if i < j. What about the MoveLeft? Can it be analyzed theoretically? Does it occur frequently in the authors’ experiments? Can the freedom that the authors have in choosing the schedule of the optimal length be used to minimize the number of left moves and, if I want to implement this approach, should I do that or would it only harm me?

- We actually included a discussion and small analysis of MoveLeft, including some empirical comparisons, in the original draft of the page. To answer your questions successively: 

  * > "does most of the reasoning ... apply to the case of MoveRight only?"

  	- Yes, due to theoretical limitations / the lack of a donor concept, the statement only holds for MoveRight. 


  * > "What about the MoveLeft? Can it be analyzed theoretically?"
  	
  	- We have a small discussion of MoveLeft in the appendix, which was moved to shorten the paper. 


> Sec. 4.1: I find the phrase ‘simulate persistence’ unusual. Does it just mean ‘compute persistent homology’? 

See the comment above. 

> As for the second test (page 28): was the Algorithm 1 used verbatim, or was the scheduling performed to move from one snapshot to the next one?

In practice we used a batch implementation, such that given an initial filtration $(K, f_0)$ and some number $b > 0$, we construct the  schedules for $(f_0, f_1)$, $(f_1, f_2)$, ..., $(f_{b-1}, f_b)$ in advance and concatenate them. The scheduling is still performed pairwise, but performed in a batch-wise manner to amortize the overhead associated with switching between scheduling and performing column operations. This was motivated by the observationt that in practice the column operations always dominated the compute time, so to maximize cache efficiency we collect as many move operations ahead of time as space allows. 

> Page 32, 4.3.1:  Is  \kappa from [10] the same as defined in Theorem 1? I suspect not, [10] says it’s coarseness. Please clarify.



>  Page 34: ‘they a convenient basis for D may be obtained’ - is this supposed to be ‘they prove that a convenient basis …’ ?voronoi  -> Voronoi
> 7-spheres volume -> 7-sphere’s volume
> In order reveal -> In order to reveal

All fixed. 

> Page 35: ‘Projecting the image data onto the first two basis vectors of leads’ - basis vectors of what?

Fixed. 

> Page 36: ‘the number of permutations applied throughout the encountered along the traversal of the dual graph’ - please fix this sentence.
> vineyards is, moves requires -> either ‘vineyards/moves algorithm/approach is/requires’, or ‘vineyards are/moves require’. Or make vineyards and moves boldface or italics, then they are visually perceived as names.
> that naively computing -> than naively computing

Page 37: if the filtrations in the parameterization family is nearby -> are nearby

Page 43 , A1.1.1 'After setting V is set' -> After setting V
'Since there exists complexes' -> Since there exists a complex / there exist complexes

Page 45. We recall an important claim .. - What is the claim? Seems to be missing, it should say something about positive/negative simplices.

Page 48. Well, in A.2.1 the authors mention \kappa as coarseness parameter! Also, the naming clash between the structure T used to compute the sorting and the barcode template T is unfortunate.

kendall -> Kendall







-----

readability - We switched the notation from symbol to permutations / indices in section 3.4, and we've reworked the pseudocode ; symbols 
on the overhead of moves - we agree that the figures may seem misleading; future work, output / geometry sensitive . Talk about balance the fraction  of kendall distances
assessment - talk abotu vineyards and non-trivial implementation details; it woudl portray vineyards inaccuractely 

Figure 1 - fixed with upper level set filtration or equiv lower filtatratin with 1 - intensity 









