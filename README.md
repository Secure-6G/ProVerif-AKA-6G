# ProVerif-AKA-6G

This work has been done by the Secure Systems Group, at the University of Helsinki, by Prof. Valtteri Niemi, Dr. Tommi Meskanen, Dr. Mohamed Taoufiq Damir, and Dr. Sara Ramezanian. For more information please visit https://link.springer.com/chapter/10.1007/978-3-031-23020-2_14.

The file $\textbf{ProVerif-Code.pv}$ contains formal verification code for an Authentication and Key Agreement (AKA) protocol for future generation of wireless mobile networks (6G). 

$\textbf{ProVerif implementation:}$

We assume that the channel between the UE and the SN is unsecure, we denote such a channel by usch. The channel between the SN and the HN is considered secure and denoted by sch. Moreover, sk_{HN}, the secret key of the HN, and the long term key K at the UE/HN are considered private at the beginning of the protocol execution. In the ProVerif code We denote sk_{HN} and K by skHN and K respectively. We use a function getkey to model the fact of retrieving the key K from the HN database based on the UE's SUPI. The symmetric primitives for authentication are refereed to by $f_1, f_2, f_3, f_4, f_5$ and SHA.

A function keyseed is used as a key derivation function. Finally, the symmetric encryption is given by the usual following form

fun senc(bitstring,bitstring):bitstring.

reduc forall m:bitstring,n:bitstring; 

sdec(senc(m,n),n)=m.
    

Next, we explain our implementation choices for the KEM and the XOR operation.

$\textbf{Modeling KEMs:}$

By definition the encapsulation algorithm Encaps, is a probabilistic algorithm taking as input a given public key and outputs a symmetric key K and a cipher text c, we capture such a property by modeling encapsulation process as three functions, a function $\textbf{Encaps}$ with inputs a variable of type public key and a freshly generated random bit string and a bit string output, 
  

fun Encaps(pubKey,bitstring):bitstring.

A function $\texttt{KEMkey}$ which takes Encaps as input and outputs the shared KEM key

fun KEMkey(bitstring):bitstring.

A function $\texttt{KEMCipher}$ which takes Encaps as input and outputs the KEM ciphertext

fun KEMCipher(bitstring):bitstring. 

The decapsulation algorithm is given by a function $\texttt{DecapsKey}$, with inputs the KEM secret key and ciphertext and output the KEM shared key

fun DecapsKey(secKey,bitstring):bitstring.
   
Finally, the KEM satisfies the following equation:

equation forall sk:secKey, r:bitstring;

DecapsKey(sk,KEMCipher(Encaps(pk(sk),r)))=

KEMkey(Encaps(pk(sk),r)).

$\textbf{Modeling the XOR operation}$

Proverif does not support associativity, i.e., $(a\oplus b)\oplus c= a\oplus (b\oplus c)$, thus, it cannot fully model operations that have this property, e.g., the XOR (exclusive or) operation. For our purpose, fortunately, we only need to model the XOR operation as a function $xor$ with two bit string inputs and one bit string output, plus an equation describing the right cancellation, namely 
$$(a\oplus b)\oplus b= a.$$
More precisely, 

fun xor(bitstring,bitstring): bitstring.

equation forall m:bitstring,n: bitstring;

 xor(xor(m,n),n)=m.
 
$\textbf{About the file Forward.pv}$

To prove forward secrecy, we assume that an attacker compromised both the HN and UE. More precisely, the attacker possesses the long term key K, sk_{HN} the HN secret key and the long term identifier SUPI. To do so we used the concept of “phases”. For more detail see Section 4.1.6 in the Proverif manual available at https://bblanche.gitlabpages.inria.fr/proverif/manual.pdf Proverif shows that the session key remains secret under the above assumptions, namely: Query not attacker_p1(Ksession[]) is true.
