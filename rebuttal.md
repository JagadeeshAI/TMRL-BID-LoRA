# Response to Reviewers — BID-LoRA (Submission 10165)

We thank all four reviewers for their careful reading and constructive feedback, and reviewer ef65 for joining the discussion. Below we respond to each point raised.

---

## Reviewer ejRW
Thank you for the detailed reading and the specific, actionable requests.

### Requested Changes

Reviewer ejRW asked us to clarify how our formulation of CLU relates to prior work. The submission read as though we were claiming to have originated the problem itself, so we've reworded the opening of Section 3 and the contributions list in the Introduction. The novelty is now pinned to the multi-cycle evaluation protocol, with the relevant prior CLU-like work cited directly rather than implied away.

Reviewer ejRW also caught a real inconsistency between the merge equation and the algorithm. Equation 1 added the forget adapter while Algorithm 1 subtracted it. Both now use the same sign, so the merged weights in the method description and the pseudocode agree.

On strengthening the unlearning evidence beyond forget accuracy, KL divergence, and a single MIA number, the point is well taken, the original evidence was thin. Table 7 now covers nine tests, including a systematic-relabelling check, a linear probe on frozen embeddings, a relearning test, three separate MIA variants, a model-inversion attack, and KL divergence against the oracle.

We've added a paragraph in the Discussion section addressing the privacy implications of the retain buffer, as requested, including why it's necessary and how consent, rather than data volume, governs what it can legitimately contain.

Reviewer ejRW noted that "first generalizable CLU benchmark" was overreaching given the scope of the evaluation. That phrase is gone, and the Introduction and Conclusion now scope our claims explicitly to vision classification and face recognition.

Finally, thank you for catching the typos, we've corrected all of them.

---

## Reviewer CDsE
Thank you for identifying the pathway and classifier-head ambiguity, and for pressing on the need for a broader impact discussion.

### Requested Changes

Reviewer CDsE asked how the three pathways, and their classifier-head parameters, are actually handled during training and after the adapters are merged. We've added a paragraph in Section 4.3 that walks through this directly. The head is a single, incrementally growing layer, gradients to it are masked per pathway exactly like the adapters, and the forget-class output nodes are deleted once merging happens.

Reviewer CDsE also flagged that our claims of privacy protection read as stronger than what the experiments actually support. The Abstract and Conclusion now state plainly that our results are empirical evidence of effective unlearning rather than a certified or formal guarantee.

On the Broader Impact Statement, requested by Reviewer CDsE, we had only a commented-out draft in the submission. There's now a full statement covering the dual-use risk of the enrollment and removal mechanism, the fact that our metrics aren't a formal guarantee, possible subgroup disparities, and governance of the retain buffer itself.

### Suggestions

Reviewer CDsE suggested stronger and more diverse attacks, along with a more comprehensive comparison against the retraining oracle. The same Table 7 added in response to reviewer ejRW covers this, adding three MIA variants, a model-inversion attack, and an explicit KL-divergence comparison against the oracle model.

---

## Reviewer KYhU
Thank you for the thorough technical scrutiny, particularly on the adapter-isolation and formulation points.

### Requested Changes

Reviewer KYhU pointed out that our original description, that each loss backpropagates exclusively through its own adapter, ignores the fact that all three adapters feed into the same activation. That was an overstatement. Section 4.3 now describes what's actually happening. Gradients do touch the shared activation, but only the parameters of the targeted adapter are ever updated, since the others are frozen at that step.

Reviewer KYhU also raised a sharp concern that zero forget accuracy could just mean the model is systematically relabelling forgotten samples into one wrong class rather than genuinely forgetting them. We've added a design-rationale explanation in Section 4.2 clarifying that the escape target is a point in embedding space, not a class label, so there's no mechanism pushing samples toward a specific wrong answer, and the new Sink and linear-probe rows in Table 7 test for this directly.

On why an MIA score near 0.5 should be read as evidence of privacy protection, and what the attack setup actually is, a question raised by Reviewer KYhU, Table 7 now spells out the full protocol, a three-layer MLP attacker run in three variants with five random initializations and 200 samples per class.

Reviewer KYhU asked us to define "irreversible" and back it with an empirical test rather than just asserting it. We now define it operationally, via how quickly the model re-learns a forgotten class from five examples compared to the oracle, in the same Table 7.

Reviewer KYhU questioned whether previously forgotten classes get re-tested at later steps. Forget accuracy is now defined cumulatively, over every class forgotten so far, in the Experiments section, rather than only over the most recently forgotten task.

Finally, Reviewer KYhU flagged a formulation inconsistency between Equations 1 and 3 and the sliding-window protocol. Equation 3c in Section 3 previously carried an index without a proper quantifier. It's now tied to the current step only, matching the pattern already used in Equation 3b.

### Suggestions

Reviewer KYhU noted that "Bi-Directional" was confusing given there are three adapters, not two. Section 4.1 now clarifies that the name refers to the two directions of knowledge flow, learning and unlearning, not the number of adapters.

Thank you for flagging the typos and incomplete sentences. These are corrected throughout.

The equation numbering that restarted partway through Section 4, raised by Reviewer KYhU, is now continuous.

Reviewer KYhU observed that t-SNE and PCA can distort distances, and that our terminology was inconsistent. We've added that caveat where the visualizations are discussed, and standardized on "escape point" throughout rather than switching to "dustbin point" partway through.

Reviewer KYhU is also right that the "Speed" column in Table 5 was a calculated figure, not a wall-clock measurement. It's now labeled accordingly, alongside actual per-step and total timing columns.

---

## Reviewer 9mgp
Thank you for pushing on the architectural justification and the breadth of evaluation.

### Requested Changes

Reviewer 9mgp asked why the tri-pathway design is principled rather than just an engineering choice to use separate adapters. This had only been shown empirically before. Section 4.2 now makes the underlying argument explicit. A single shared adapter forces retention, acquisition, and forgetting to compete for the same low-rank subspace, which is a genuine source of gradient conflict, and separating them into disjoint subspaces removes that conflict by construction rather than by tuning.

Reviewer 9mgp asked for relearning, model-inversion, extraction, and adaptive membership-inference attacks. All four now appear as rows in the same Table 7.

On the practical limitation of a 10% retain buffer in privacy-sensitive settings, raised independently by Reviewer 9mgp, we've added the same discussion in Section 8.1 that addresses reviewer ejRW's related point.

Reviewer 9mgp asked us to either broaden the evaluation beyond our two original benchmarks or be explicit about the limits of what we're claiming. We did both. Face recognition now runs on VGGFace2 in Table 2 rather than the smaller original set, and the Introduction and Experiments section state clearly that our claims are scoped to the vision-classification and face-recognition settings we actually test.

---

## Reviewer ef65
Thank you for the concise and pointed review.

### Requested Changes

Reviewer ef65 noted that BID-LoRA still depends on a retain buffer, which limits how close it comes to truly data-free unlearning. We don't disagree. Section 8.1 argues that the buffer is currently unavoidable for non-convex models, but grounds its use in revocable subject consent rather than raw retention volume, and the Conclusion names eliminating the buffer entirely as future work.

Reviewer ef65 pointed out that the evaluation was limited to two vision benchmarks under a fixed six-task setting. The six-task ceiling no longer holds across the board, since face recognition now runs across twelve sequential cycles on VGGFace2 in Table 2, double the length of the original protocol. The evaluation is still confined to two domains, vision classification and face recognition, and we haven't claimed otherwise.

As Reviewer ef65 observed, the parameter efficiency comes with a real performance cost. The Results Discussion in the Experiments section now says so directly, pointing to Tables 1 and 2. Baselines updating all of their parameters occasionally match or beat BID-LoRA's overall accuracy on individual tasks, at roughly twenty times the tunable-parameter cost.
