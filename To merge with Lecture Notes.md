We need to check if:
- Move(B) is executable in S0;
- move(C) is executable in the state that follows move(B),S0
- move(D) like before;

We call:
- $S_1 = do(move(B),S_0)$;
- $S_2 = do(move(C),S_1)$;

**1st condition**
$D\models Poss(move(B),S_0)$
$R[Poss(move(B),S_0)] = Poss(move(B),S_0)$
$D\models Poss(move(B),S_0) \iff D_{S_0}\cup D_{UNA}\models R[Poss(move(B),S_0)]$
and since $R[Poss(move(B),S_0)] =Poss(move(B),S_0)\iff \exists y.AgentAt(y,S_0)\wedge Right(B,y)$
So:
$R[Poss(move(B),S_0)] = R[\exists y.AgentAt(y,S_0)\wedge Right(B,y)] =$
$= \exists y.R[AgentAt(y,S_0)\wedge Right(B,y)]$=
$=\exists y.R[AgentAt(y,S_0)]\wedge R[Right(B,y)]=$
$=\exists y.AgentAt(y,S_0)\wedge Right(B,y)]$
Now we can apply the regression theorem:
$D_{S_0}\cup D_{UNA} \models \exists y.AgentAt(y,S_0)\wedge Right(B,y)$
and since $D_{S_0}$ contains this axioms:
$AgentAt(x,S_0)\iff x=A$
and
$Right(x,t)\iff (x=A\wedge y=B) ecc...$
So the first condition is executable!

**2nd condition**
$D\models Poss(move(C),S_1)$ ?
$R[Poss(move(C),S_1)] = R[Poss(move(C),do(move(B),S_0))]$
![[Pasted image 20250426195247.png]]
So we replace into the regression formula:
$R[\exists y.AgentAt(y,do(move(B),S_0))\wedge Right(y,C)]$
And then we split all the regression like before.