Evaluation of thermal conductivity (Matlab files are in Codes1.zip archive)_

matlab file: ThermCondKinksRealDefRestr.m - calculates thermal conductivity as integrated transmission for strongly aligned chains. 
ThermCondKinksRealRWDef.m - calculates thermal conductivity as integrated transmission for random walk chains. 

function: ThermCondKinksRealDefRestr(l, k, A, B, ph, ph1, n1, n2, xi, Ym) 
function: y=ThermCondKinksRealRWDef(l, k, A, B, ph, ph1, n1, n2, xi)
Arguments: 
l - average spacing between kinks. l=2 and 4 were probed
k - number of kinks. It must be divisor of 4 
A - force constant between nearest neighboring atoms (4.8) 
B - force constant between next neighboring atoms (1) 
ph - inter-bond angle usually set to 0.7854 radian 
ph1 - axis turn angle in the kink position 
n1, n2 the numbers of initial and final randomly generated sets
xi - ratio of force constants for next neighbors at the kink position and in the regular chain - set to 1. 
Ym - maximum vertical displacement (Ym=2 or Ym=4 chain periods)
Outcome:
y.k - averaged transmission, y.Dk - relative error of the definition

function: 

Used functions: 
GenerateRestrKinksCoord.m - generates random coordinates of the chain atoms within the limited range of vertical displacements
GenerateRealKinksRWCoord.m - generates random coordinates of the chain atoms randomly 
BlocksCoordDef.m - calculates Hessian matrix and matrices, characterizing coupling with leads.
TransmVect.m evaluates transmissions for the sequence of frequencies Om

Evaluation of phonon scattering by kinks (Matlab files are in Codes2.zip archive)_

matlab function: [y, Tst, Trl, Trt, z]=Transmission(om, Vl, Vr, H, A, B, ph) calculates phonon transmissions and reflections at given frequency (om) through the defect domain characterized by coupling with leads Vl and Vr for the fence model using advanced Green function method. 
A - force constant between nearest neighboring atoms (4.8) 
B - force constant between next neighboring atoms (1) 
ph - inter-bond angle usually set to 0.7854 radian 
Transmission and reflection coefficients are given by two column vectors z.TRL and z.TRT for transmissions and reflections of LA and TA phonons, respectively. Two top parameters are transmissions and two bottom parameters are reflections with outgoing LA or TA phonons, respectively. 

Single kink parameters are generated using the function 
BlocksCoordGenRot(A, B, ph, ph1, n),  
Parameters A, B, ph, ph1 are defined as above
n - even number exceeding 4 indicating how many sites of leads to include. 

The parameters for transmission calculations (Transmission(om, Vl, Vr, H, A, B, ph)) are defined using the function outcome yz=BlocksCoordGenRot(A, B, ph, ph1, 6) 
as H=yz.Hin; Vl=yz.Vl; Vr=yz.Vr; 
