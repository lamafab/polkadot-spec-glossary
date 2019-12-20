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
|$\mathcal N$|the set of the nodes Polkadot state trie||
|$N$|an individual node in the trie|$N \in \mathcal N$|
|$\mathcal N_b$|a branch node which has one child or more (max 16)|$\mathcal N_b:=\{N \in \mathcal N \mid \text{N is a branch node}\}$|
|$\mathcal N_l$|a leaf node is a childless node|$\mathcal N_l:=\{N \in \mathcal N \mid \text{N is a leaf node}\}$|
|$SV_N$|the subvalue of the given node|$SV_N:=\left\{\begin{array}{l l}Enc_{SC}(StoredValue(k_N)) & \text{N is a leaf node}\\ChildrenBitmap(N)\|Enc_{SC}(H(NC_1))...Enc_{SC}(StoredValue(k_N)) & \text{N is a branch node}\end{array}\right.$|
|$StoredValue$|function to retrieve the value stored under a specific key in the state storage|$\left.\begin{array}{l c}StoredValue & \mathcal K \to \mathcal V\\& k \to \left\{\begin{array}{l c}v&if(k,v)\text{ exists in state storage}\\\phi&otherwise\end{array}\right.\end{array}\right.$<br><br>where $K \subset \mathbb B$ and $\mathcal Vw \subset \mathbb B$ are respectively the set of all keys and values stored in the state storage|
|$KeyEncode(k)$<br>$k_{enc}$|function to encode keys for labeling brnaches of the Trie|$k_{enc}:=(k_{enc1},...,k_{enc2n}):=KeyEncode(k)$<br>such that:<br>$KeyEncode(k):\left\{\begin{array}{l l l}\mathbb B & \to & Nibbles^4\\k:=(b_1,...,b_n) := & \to & (b^1_1,b^2_1,b^1_2,b^2_2,...,b^1_n,b^2_n)\\ & \to &:=(k_{enc_1},...,k_{enc2n})\end{array}\right.$<br>where $Nibble^4$ is the set of all nibbles of 4-bit arrays and $b^1_i$ and $b^2_i$ are 4-bit nibbles, which are the big endian representation of $b_i$:<br>$(b^1_i,b^2_i):=(b_i/16,b_i,mod16)$<br>where mod is the remainder and / is the integer division operators.|
|$pk_N$|TODO||
|$pk^{Agr}_N$|TODO||
|$HeadN$|the node header of node $N$||
|$v_N$|the node value which is stored by the node $N \in \mathcal N$|$v_N := Head_N\parallel Enc_{HE}(pk_N)\parallel SV_N$|
|$ChildrenBitmap$||$ChildrenBitmap:\left.\begin{array}{l}\mathcal N_b \to \mathbb B_2\\N \to (b_{15},...,b_8,b_7,...b_0)_2\end{array}\right.$<br>where<br>$b_i:=\left\{\begin{array}{l c}1 & \exists N_c \in \mathcal N:k_{Nc} = k_{N_b} \parallel i \parallel pk_{Nc}\\0 & otherwise\end{array}\right.$|
|$H(N)$|the Merkle value of $N$|$\left.\begin{array}{l}H:\mathbb B \to \mathbb B_{32}\\H(N):\left\{\begin{array}{l l}v_N & \parallel v_N \parallel < 32\\Blake2b(v_N) & \parallel v_N \parallel \geq 32\end{array}\right.\end{array}\right.$<br><br>Where $v_N$ is the node value of $N$ and $0_{32-\parallel v_N \parallel}$ an all zero byte array of length $32-\parallel v_N \parallel$. The Merkle hash of the Trie is defined as: $Blake2b(H(R))$ where $R$ is the root of the Trie.|

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
|$Enc^{Len}_{SC}$|SCALE length encoding aka. compact encoding of non-negative interger numbers of varying sized prominently in an encoding length of arrays|$Enc^{Len}_{SC}: \mathbb N \to \mathbb B \newline n \to b\left\{\begin{array}{l l}l_1 & 0 \leq n < 2^6\\i_1i_2 & 2^6 \leq n < 2^{14} \\j_1j_2j_3 & 2^{14} \leq n < 2^{30} \\k_1k_2...k_m & 2^{30} \leq n\end{array}\right.$<br>in where the lest significant bits of the first byte of byte array $b$ are defined as follows:<br>$\left.\begin{array}{r r}l^1_1l^0_1 = 00\\i^1_1i^0_1 = 01\\j^1_1j^0_1 = 10\\k^1_1k^0_1 = 11\end{array}\right.$<br>and the rest of the bits of $b$ store the value of $n$ in little-endian format in base-2 as follows:<br>$\left.\begin{array}{l l}l^7_1...l^3_1l^2_1 & n < 2^6\\i^7_2...i^0_2i^7_1...i^2_1 & 2^6 \leq n < 2^{14}\\j^7_4...j^0_4j^7_3...j^7_1...j^2_1 & 2^{14} \leq n < 2^{30}\\k_2+k_32^8+k_42^{2\times8}+...+k_m2^{(m-2)\times8} & 2^{30} \leq n\end{array}\right\}:=n$<br>such that:<br>$k^7_1..k^3_1k^2_1:=m-4$|

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

# Hex encoding

|Symbol|Description|Defined|
|-|-|-|
|$Enc_{HE}(PK)$|hex encoding|$Enc_{HE}(PK) := \left\{\begin{array}{l l}Nibbles_4 \to \mathbb B\\PK=(k_1,...,k_n)\to\left\{\begin{array}{l l}(16k_1+k_2,...,16k_{2i-1}+k_{2i}) & n = 2i\\(k_1,16k_2+k_3,...,16k_{2i}+k_{2i+1}) & n = 2i+1\end{array}\right.\end{array}\right.$|

# Misc (TODO...)

|Symbol|Description|Defined|
|-|-|-|
|$H_h(B)$|Block hash||
|$H_i(B)$|Block number||
