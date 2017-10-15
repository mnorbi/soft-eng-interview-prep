# Cryptography
- Symmetric
- Assymetric
- Side channel
    - example: cpu heath generation to analyse algoithm
- Kerchoff principle
    - key should be secret, algorithm should be public
- Perfect cipher
 - P(msg=guess|cipher text) = P(msg=guess)
- OneTimePassword 
 - xor msg with a mask, never reuse mask
 - is provably perfect
 - malleable
   - example:
      - given msg is 'Y' or 'N'
      - attacker can flip message bit with the xorDiff('Y','N')
      - fix with nonce+hash
      - multiple symbols for y/n with differing xorDiff
 - man in the middle attack possible
 - impractical
     - keySize >= msgSize, keyCnt >= msgCnt
     - difficult to distribute keys
 - Lorenz cipher
     - WWII Axis crypto system
     - was cracked by U.S. cryptanalysis
     - crack start:
        - two different messages were intercepted encrypted with the same OTP key effectively providing (msgA xor msgB)
        - this was enough to reverse engineer the structure, taking 6 month job
     - structure
        - 5 bit input channel
        - K wheels
            - on each channel with different sizes
            - stepping at each new bit per channel
            - K wheel repetition cycle was big, but between 2 channels only 1.3K (wheelSize1*wheelSize2 = 41*31), bruteforceable
        - S wheels
            - on each channel
            - each either stepping together or not based on two M wheels
        - cThChannelOutput for the i-th character Z(c,i) = msgBit(c,i) xor kWheel(c,i) xor sWheel(c,i)
     - double xor delta cracking aproach
        - exploit:
            -  S wheel weakness
                 - don't advance 50% of the time
                 - when advance, sBit is 50% of the time 1, 50% of te time 0
            - german language repetition structure
                 - 3.3% of digraphs are repetitions
        - first delta is between one channel's two subsequent outputs, to "cancel out" sWheelBits
           - deltaZ(c,i) = Z(c,i) xor Z(c,i+1)
        - second delta is between two subsequent channels to "cancel out" german language repetition
        - first and second together
           - doubleDeltaZ(c,i) = deltaZ(c,i) xor deltaZ(c+1,i) = doubleDeltaM(c,i) xor doubleDeltaK(c,i) xor doubleDeltaS(c,i)
        - probability(doubleDeltaM) = 0.61, probability(doubleDeltaS) = 0.73
        - bruteforce K on pool of inercepted messages and try to detect K correctness
        - probabilityOf(doubleDeltaZ=0|given correctly removed K bits) = 
             probability(doubleDeltaM=0)*probability(doubleDeltaS=0) +
             probability(doubleDeltaM=1)*probability(doubleDeltaS=1) = 0.61*0.73+(1-0.61)*(1-0.73) = 0.55 !!which is not 0.5, more than enough to use for good K detection!!
        - try to detect good K when prob(dDZ=0|removedKBits) results in 0.55
Shanon's Keyspace Theorem
  - every practical cypher is non perfect
  - if keySpaceSize < msgSpaceSize, then the cypher is not perfect, p(m=m*|intercepted msg) != p(m=m*) for some m*
  - proof aproach: model a brute force attack by brute force decrypting an intercepted msg, the image of the decryption is smaller than the msgSpace so will exclude some message
symmetric ciphers
  block cipher
  stream cipher
  DES, 3DES
  AES
    NIST, Rijndael
  ECM - electronic codebook mode
    reordering unsafe
    same output for same block
  CBC - cipher block chaining
    fixes ECM problems
  CTR - counter mode
    parallelizable
  CFB - cipher feedback mode
  OFB - output feedback mode
randomness
  statistical test analysis
    cannot prove randomness, at most disprove it
  generation
    unpredictability
    random pool source ---as key into--->|AES-128 encryptor--->
                       --1...n as input->|
	AES-128 is symmetric, generates random values too infequently
        encryptor output should be used sometimes as key, instead of original seed
fortune prng
kolmogorov complexity
  berry paradox
cryptographic hash functions
  regular hash requirements
    compression
    distribution
  cryptographic requirements
    preimage resistance
    second preimage resistance/weak collision resistance
      brute force expected N trials
    strong collision resistance/collision resistance
      SHA-1 not resistent
      brute force expected sqrt(N) trials
  CBC-MAC
    with invertible function it's bad
    https://discussions.udacity.com/t/unit2-20-quiz-mistake-cbc-mac-is-not-collision-resistant/74573
  random oracle
  birthday "paradox"
    hash functions need large output
    SHA-1 160 bits: breakable with 2**51 operations
    SHA-2 256/512 bits
    SHA-3 Keccak
  dictionary attack
  salt
    against dictionary attacks
  hash chain
    S/Key password system
