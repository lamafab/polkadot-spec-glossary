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


|||
|-|-|
|$k^{pr}_v$|Private key|
|$v_{id}$|

|||
|-|-|
|$H_h(B)$|Block hash|
|$H_i(B)$|Block number|
