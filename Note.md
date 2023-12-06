# 9. Simply Typed Lambda-Calculus

* Type context $\Gamma$

    * $\Gamma$ 是二元关系 $t:T$ 的集合，或者可以理解为 `term => type` 的字典

* Type relation

    * Type relation 确定了此 calculus 是 well-typed 的。即对于任意 term `t`，在一定的 context $\Gamma$ 下，都能找到 type `T` 使得 $\Gamma\vdash t:T$
    * Type relation 是一个三元组，因为它需要一系列 assumption (context) 来确定 `t` 中所有 free var 的 type

* T-Abs 的理解
    $$
    \frac{\Gamma,x:T_1\vdash t:T_2}{\Gamma\vdash \lambda x:T_1.t:T_1\to T_2}
    $$

    * 对于 premise：在 $\Gamma \cup \{x:T_1\}$ 这样的 context 下，有 $t:T_2$
        * 我们先**不加说明地**给出一个 type assumption $x:T_1$ ，`x` 不在 `t` 中出现，是 `t` 的 free var。这样做是为了我们之后能写出带 annotation 的 lambda
        * 这里的 $\Gamma$ 可能包含 $t$ 中的 nested variables 的类型信息，所以不能省略
    * 对于 conclusion：在 $\Gamma$ 这样的 context 下，我们可以给出 simply typed (params with type annotation) 的一个 lambda application 表达式 $\lambda x:T_1. t$，整个表达式的类型是 $T_2$
        * （**重要**）这里我们将 $x:T_1$ 从 context 中移除，是因为 $x$ 不再是 free var

* T-Var 的理解
    $$
    \frac{x:T\in\Gamma}{\Gamma\vdash x:T}
    $$

    * 如果我们已经给出了 assumption `x:T`，那么 term `x` 的类型就是 `T`
    * （**重要**）参考 type relation，任何一个 term 的类型的确定，一定需要 $\Gamma$
    * 注意此处的逻辑顺序：一定是先**不加说明地**给出 assumption，才能确定 term 的 type。**Term 不是天生就拥有 type 的**
    * 这里的“不加说明”可以理解为在逻辑系统之外，来自于用户手动指定的语义

* T-App，无需赘言

    <img src="D:\Document\doc\TAPL\assets\image-20231206223007907.png" alt="image-20231206223007907" style="zoom: 50%;" />

    

# 15. Subtyping

* Calculus $\lambda_{<:}$ , simply typed lambda-calculus with subtyping

* `S` in a subtype of `T`, written as `S <: T`

* Subsumption rule, `Gamma|-t:S  S<:T  /  Gamma|-t:T `

    * upcast
    * 子类型是限制更多的类型，例如 `{x:int, y:int} <: {x:int}`

* The subtype relation is

    * Reflective, `S<:S`
    * Transitive, `S<:U  U<:T  /  S<:T`
    * **Not** anti-symmetric, `{x:bool, y:int} <: {y:int, x:bool}` and vise versa

* **Arrow Rule**
    $$
    \frac{T_1<:S_1\ \ \ \ S_2<:T_2}{S_1\to S_2<: T_1\to T_2}
    $$

    * Argument types: Contravariant
    * Return types: Covariant
    * 函数 `f:S1->S2` 若能接收 `S1` 类型的参数，那它一定能接收 `S1` 的子类型 `T1` 的参数；
    * 函数 `f` 的返回值若是 `S2` 类型，i.e. 能被 `S2` 类型的变量接收，那它一定也能被 `S2` 的父类型 `T2` 的变量接收。

* Note: 我们一般不认为 `T1*T2 <: T1`，但却有 `{x:T, y:U} <: {x:T}` 。这是因为 record 类型中每个 entry 都有对应的 key，但 tuple 的 entry 只与 index 相关。

    * 你也不想允许 `(4, 'hello') + 1 == 5` 吧

* Bottom

    * rule: ` / Bot <: T`
    * `Bot` type is empty. There is no closed values of type `Bot`。这是显然的，否则若 value `v : Bot`，那么对于任意 `T` 都有 `v : T`，构造一个反例：`v:int` 并且 `v:string`
    * 允许有 term `t : Bot`，只要这个 `t` 不继续被 evaluate，eg: panic，这样使得 `if x then 1 else panic` 能够有类型 `int`
    * 会使得 type checker 复杂化。不能直接从 term 推断类型（eg，对于 term `t x`，不能再假定 `t` 是 arrow type，因为也可能是 Bot）

    

