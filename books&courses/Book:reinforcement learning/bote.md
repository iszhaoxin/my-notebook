$$
\begin{aligned}
q_{\pi}	&=\mathbb{E}_{\pi}[G_t|S_t=s, A_t=a]\\
		&=\mathbb{E}_{\pi}\large[\sum_{k=0}^{\infin}\gamma^kR_{t+k+1}|S_t=s, A_t=a]\\
		&=\sum_{s',r}p(s',r|s,a)\large[r+\gamma v_{\pi}(s')], \ \operatorname{for all} s \in \mathcal{S}\\
		&\large({\gamma=1,\ r=-1,\ p(s',r|s,a)=1)}
\end{aligned}
$$


$$
\begin{aligned}
q_{\pi}(11,down)
		&=\sum_{s',r}p(T,-1|11,down)\large[r+\gamma v_{\pi}(s')]\\
		&=1*[-1+1*0]=-1\\
\end{aligned}
$$

$$
\begin{aligned}
q_{\pi}(7,down)
		&=\sum_{s',r}p(11,-1|7,down)\large[r+\gamma v_{\pi}(s')]\\
		&=1*[-1+1*-14]\\
		&=-15\\
\end{aligned}
$$

$$
\begin{aligned}
q_{k+1}(s,a)	&=\mathbb{E}_{\pi}[G_t|S_t=s, A_t=a]\\
		&=\mathbb{E}_{\pi}\large[\sum_{k=0}^{\infin}\gamma^kR_{t+k+1}|S_t=s, A_t=a]\\
		&=\sum_{s',r}p(s',r|s,a)\large[r+\gamma v_{\pi}(s')], \ \operatorname{for all} s \in \mathcal{S}\\
		&=\sum_{s',r}p(s',r|s,a)\large[r+\gamma \sum_a\pi(a'|s')q_{k}(s',a')], \ \operatorname{for all} s \in \mathcal{S}\\
		&=\sum_a\pi(a'|s')\sum_{s',r}p(s',r|s,a)\large[r+\gamma q_{k}(s',a')], \ \operatorname{for all} s \in \mathcal{S}\\
\end{aligned}
$$

$$
\begin{aligned}
q_{\pi}(s,a)	&=\mathbb{E}_{\pi}[G_t|S_t=s, A_t=a]\\
		&=\sum_a\pi(a'|s')\sum_{s',r}p(s',r|s,a)\large[r+\gamma q_{\pi}(s',a')], \ \operatorname{for all} s \in \mathcal{S}\\
\end{aligned}
$$

