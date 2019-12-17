|Symbol|Description|Defined|
|-|-|-|
|$v$|GRANDPA Voter|
|$\mathbb{V}$|set of all GRANDPA voters|
|$\mathbb{V}_B$|set of all GRANDPA voters for a given block|
|$\mathbb{V}_{id}$|...|
|$GS$|GRANDPA state|$GS := \{\mathbb{V},id_\mathbb{V}, r\}$|
|$V(B)$|GRANDPA vote|$V(B) := (H_h(B),H-I(B))$|
|$V^{r,pv}_v$|pre-vote|
|$V^{r,pc}_v$|pre-commit|
|$r$|Voting round number|
|$V_id$|Incremental counter tracking membership|
|$V^{r,stage}_v(B)$|equivocatory vote||
|$\mathcal{E}^{r,stage}$|set of all equivocators voters in sub-round "stage" of round $r$||
|$\mathcal{E}^{r,stage}_{obs(v)}$|set of all equivocators voters in sub-round "stage" of round $r$ observed by voter $v$||
|$VD^{r,stage}_{obs(v)(B)}$|the set of observed direct votes for block $B$ in round $r$|
|$V^{r,stage}_{obs(v)}$|the set of total votes observed by voter $v$ in sub-round "stage" of round $r$|
|$V^{r,stage}_{obs(v)}(B)$|set of all observed votes by $v$ in the sub-round stage of round $r$ for block $B$|$V^{r,stage}_{obs(v)}(B) := \displaystyle\bigcup_{v_i \in \mathbb{V}, B \geq B'} VD^{r,stage}_{obs(v)}(B')$|
|$B^{r,pv}_v$|The current pre-voted block|$H_n(B^{r,pv}_v) = Max(H_n(B)\|\forall B :\#V^{r,pv}_{obs(v)}(B)\geq2\setminus3\|\mathbb{V}\|)$|
|-|-|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

# Voting Messages Specification

|Symbol|Description|Defined|
|-|-|-|
|$M^{r,stage}_v$|A broadcasted message by the voter $v$ casting his vote to the network|$M^{r,stage}_v := Enc_{SC}(r,id_\mathbb{V},Enc_{SC}(stage,V^{r,stage}_v,Sig_{ED25519}(Enc_{SC}(stage,V^{r,stage}_v,r,V_{id}),v_{id})))$|
|$J^r(B)$|The justification for block $B$ in round $r$|The justification is a vector of pairs of the type $(V(B'),(Sign^{r,pc}_{vi}(B'),v_{id}))$ in which either $B'\geq B$ or $V^{r,pc}_{vi}(B')$ is an equivocatory vote|
|$Sign^{r,pc}_{vi}(B')$|The signature of voter $v$, broadcasted during the pre-commit sub-round of round $r$|
|$M^{r,Fin}_v(B)$|The finalizing message broadcasted by voter $v$ to the network indicating that voter $v$ has finalized bock $B$ in round $r$|$M^{r,Fin}_v(B) := Enc_{SC}(r,V(B),J^r(B))$|

# Cryptographic keys

|Symbol|Description|Defined|
|-|-|-|
|Account key $(sk^a, pk^a)$|A keypair of type of either SR25519, ED25519, secp256k1||

# SCALE Codec

|Symbol|Description|Defined|
|-|-|-|
|$A$|Byte array|$A := b_1, b_2, ... b_n$|
|$T$|Tuple where $A_i$'s are values of different types|$T := (A_1, ..., A_n)$|
|$S$|Sequence where $Ai$'s are values of the same type (and the decoder is unable to infer value of $n$ from the context)|$S := A_1, ..., A_n$|
|$\tau$|Varying data type (TODO)|$T = \{T_1, ..., T_n\}$|
|$Enc_{SC}(A)$|SCALE encoding of byte array $A$ such that $n < 2^{256}$|$Enc_{SC}(A) := Enc^{Len}_{SC}(\parallel A \parallel)\parallel A$|
|$Enc_{SC}(T)$|SCALE encoding of tuple $T$|$Enc_{SC}(T) := Enc_{SC}(A_1)\parallel Enc_{SC}(A_2)\parallel ... \parallel Enc_{SC}(A_n)$|
|$Enc_{SC}(S)$|SCALE encoding of sequence $S$|$Enc_{SC}(S) := Enc^{Len}_{SC}(\parallel S \parallel) Enc_{SC}(A_1)\mid Enc_{SC}(A_2)\mid ... \mid Enc_{SC}(A_n)$|
|$Enc^{Len}_{SC}$|SCALE length encoding aka. compact encoding of non-negative interger numbers of varying sized prominently in an encoding length of arrays|$Enc^{Len}_{SC}: \mathbb N \to \mathbb B \\n \to b\begin{Bmatrix}l_1 && 0 \leq n < 2^6\\i_1i_2 && 2^6 \leq n < 2^{14} \\j_1j_2j_3 && 2^{14} \leq n < 2^{30} \\k_1k_2...k_m && 2^{30} \leq n\end{Bmatrix}$|

|||
|-|-|
|$k^{pr}_v$|Private key|
|$v_{id}$|

|||
|-|-|
|$H_h(B)$|Block hash|
|$H_i(B)$|Block number|
