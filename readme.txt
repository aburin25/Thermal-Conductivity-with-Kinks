Evaluation of thermal conductivity (Matlab files are in Codes.zip archive)_

matlab file: ThermCondKinksRealDefRestr.m - calculates thermal conductivity as integrated transmission. 

function: ThermCondKinksRealDefRestr(l, k, A, B, ph, ph1, n1, n2, xi, Ym) 
Arguments: 
l - average spacing between kinks. l=2 and 4 were probed
k - number of kinks. It must be divisor of 4 
A - force constant between nearest neighboring atoms (4.8) 
B - force constant between next neighboring atoms (1) 
ph - inter-bond angle set to 0.7854 radian 
ph1 - axis turn angle in the kink position 
n1, n2 the numbers of initial and final randomly generated sets
xi - ratio of force constants for next neighbors at the kink position and in the regular chain - set to 1. 
Ym - maximum vertical displacement (Ym=2 or Ym=4 chain periods)
Outcome:
y.k - averaged transmission, y.Dk - relative error of the definition

Used functions: 
GenerateRestrKinksCoord.m - generates random coordinates of the chain atoms 
BlocksCoordDef.m - calculates Hessian matrix and matrices, characterizing coupling with leads.
TransmVect.m evaluates transmissions for the sequence of frequencies Om
