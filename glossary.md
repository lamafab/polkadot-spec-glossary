# Basics
|Symbol|Description|Defined|
|-|-|-|
|$b$|a sequence of bytes of length $n$|$b:=(b_0,b_1,...,b_{n-1})$ such that $0 \leq b_i \leq 255$|
|$\mathbb B_n$|the set of all byte arrays of length $n$|$\mathbb B:=\displaystyle\bigcup^\infty_{i=0} \mathbb B_i$|
|$I$|little-endian representation of a non-negative integer|$I=(B_n...B_0)_{256}$|
|$B$|byte array|$B = (b_0,b_1,...,b_n)$ such that $b_1:=B_i$|
|$Enc_{LE}$||$Enc_{LE}:\left.\begin{array}{l l l}\mathbb Z+ & \to & \mathbb B\\(B_n...B_0)_{256} & \to & (B_0,B_1,..., B_n)\end{array}\right.$|
|$C$|a blockchain is a directed path graph. Each node of the graph is called Block and indicated by $B$||
|$P(B)$|the parent of block $B$|$B_{n+1}:=P(B_n)$|

# Block Format
|Symbol|Description|Defined|
|-|-|-|
|$H_p$|the 32-byte Blake2b hash of the header of the parent of the block||
|$H_i$|the interger representing the index of the current block in the chain. It is equal to the number of the ancestor blocks. The genesis block has number 0||
|$H_r$|the root of the Merkle trie, whose leaves implement the storage for the system||
|$H_e$|the field which is reserved for the Runtime to validate the integrity of the extrinsics composing the block body. The extrinsics_root is set by the runtime and its value is opaque to Polkadot RE||
|$H_d$|used to store any chain-specific auxiliary data|$H_d(B):=H^1_d,...,H^n_d$ where $H^i_d$'s are digest items|
|$H_h(B)$|the hash of the header of block $B$ by codec|$H_h(B):=Blake2b(Enc_{SC}(Head(B)))$|
|$Body(B)$|the body of block $B$|$Body(B):=Enc_{SC}(E_1,...,E_n)$ where each $E_i \in \mathbb B$ is a SCALE encoded extrinsic|

# GRANDPA

|Symbol|Description|Defined|
|-|-|-|
|$v$|GRANDPA Voter|
|$k^{pr}_v$|ED25519 private key of $v$||
|$v_{id}$|ED25519 public key of $v$||
|$\mathbb{V}$|set of all GRANDPA voters|
|$\mathbb{V}_B$|set of all GRANDPA voters for a given block|
|$\mathbb{V}_{id}$|is an incremental counter tracking membership, which changes in $V$|
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
|$Enc^{Len}_{SC}$|SCALE length encoding aka. compact encoding of non-negative interger numbers of varying sized prominently in an encoding length of arrays|$Enc^{Len}_{SC}: \mathbb N \to \mathbb B\\n \to b\left\{\begin{array}{l l}l_1 & 0 \leq n < 2^6\\i_1i_2 & 2^6 \leq n < 2^{14} \\j_1j_2j_3 & 2^{14} \leq n < 2^{30} \\k_1k_2...k_m & 2^{30} \leq n\end{array}\right.$<br>in where the lest significant bits of the first byte of byte array $b$ are defined as follows:$\\\left.\begin{array}{r r}l^1_1l^0_1 = 00\\i^1_1i^0_1 = 01\\j^1_1j^0_1 = 10\\k^1_1k^0_1 = 11\end{array}\right.$<br>and the rest of the bits of $b$ store the value of $n$ in little-endian format in base-2 as follows:$\\\left.\begin{array}{l l}l^7_1...l^3_1l^2_1 & n < 2^6\\i^7_2...i^0_2i^7_1...i^2_1 & 2^6 \leq n < 2^{14}\\j^7_4...j^0_4j^7_3...j^7_1...j^2_1 & 2^{14} \leq n < 2^{30}\\k_2+k_32^8+k_42^{2\times8}+...+k_m2^{(m-2)\times8} & 2^{30} \leq n\end{array}\right\}:=n$<br>such that:$\\k^7_1..k^3_1k^2_1:=m-4$|

# Hex encoding

|Symbol|Description|Defined|
|-|-|-|
|$Enc_{HE}(PK)$||$Enc_{HE}(PK) := \left\{\begin{array}{l l}Nibbles_4 \to \mathbb B\\PK=(k_1,...,k_n)\to\left\{\begin{array}{l l}(16k_1+k_2,...,16k_{2i-1}+k_{2i}) & n = 2i\\(k_1,16k_2+k_3,...,16k_{2i}+k_{2i+1}) & n = 2i+1\end{array}\right.\end{array}\right.$|

# Misc (TODO...)

|||
|-|-|

|||
|-|-|
|$H_h(B)$|Block hash|
|$H_i(B)$|Block number|
