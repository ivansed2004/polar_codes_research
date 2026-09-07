# **Data analysis of LLR and Machine Learning model to predict bit error numbers in polar code decoder for AWGN channel**

## Glossarium

1. AWGN -> Additive White Gaussian Noise
2. B-DMC -> Binary-input Discrete Memoryless Channel
3. FEC -> Forward Error Correction
4. LDPC -> Low Density Parity Check
5. LLR -> Log-Likelihood Ratio
6. PBCH -> Physical Broadcast CHannel
7. PDCCH -> Physical Downlink Control CHannel
8. PUCCH -> Physical Uplink Shared CHannel
9. PDSCH -> Physical Downlink Shared CHannel
6. PUSCH -> Physical Uplink Control CHannel
8. SCL -> Successive Cancellation List
9. UE -> User Equipment

## Introduction

**Polar codes** are block codes that have been proposed by Erdal Arikan, IEEE Senior Member, in 2009 [1].
The core concept of polar codes is **CHANNEL POLARIZATION**. The Theorem of Channel Polarization [1] proves that by applying a recursive linear transformation to $N = 2^n$ independent copies of a B-DMC $W$, the synthesized bit-channels $W_N^{(i)}$ split into two extremes as $N \to \infty$:
1. **Perfect Channels**: A fraction of subchannels becomes completely noiseless, with symmetric capacity $I(W_N^{(i)}) \to 1$.
2. **Useless Channels**: The remaining subchannels become completely noisy, with capacity $I(W_N^{(i)}) \to 0$.

The fraction of Perfect Channels acquires the capacity $I(W)$ of the original channel $W$. The capacity $I(W)$ to be achieved simply by sending data over the good subchannels and freezing the bad ones. Roughly, the mathematics of B-DMC allows for "squeezing" the ideal channels from the original channel leaving the useless ones out of communications.

## Applications & Use Cases

Polar codes are successfully applied for Control and Broadcast Channels (PDCCH, PUCCH, PBCH) [2] in 5G radio interface due to their superior performance on short payloads (up to N = 1024 bits) and SCL decoding complexity as $O(N*logN)$ [1], while LDPC codes handle large-block Shared Channels (PDSCH, PUSCH) to maximize Shannon capacity.

## References

**[1] Channel polarization: A method for constructing capacity-achieving codes for symmetric binary-input memoryless channels**

The fundamental Arikan's work introducing polar codes. The author dives deep into Shannon's information theory, FEC, introduces channel polarization as a concept, derives the Shannon channel capacity expression $I(W)$ for the exact polarized channel and, finally, demonstrates the encoder and decoder algorithm structures.

> **Source:** https://arxiv.org/pdf/0807.3917

**[2] 3GPP TS 38.212 "Multiplexing and channel coding"**

The specification defines the detailed implementation of physical layer multiplexing and channel coding in 5G NR. Section 5 specifies the general mathematical operations and coding schemes, including LDPC codes, Polar codes, and short block codes. Both Section 6 and Section 7 specify the transport channel and control information processing for the uplink and downlink respectively. With respect to polar codes, all the control information is coded by them.

> **Source:** https://www.etsi.org/deliver/etsi_ts/138200_138299/138212/17.10.00_60/ts_138212v171000p.pdf

**[3] Successive Cancellation List Polar Decoder using Log-likelihood Ratios**

The enhanced decoding algorithm compared to standard SC (Successive Cancellation). Involves LLR instead of separated Likelihood functions for '0' and '1'. The article proposes the algorithm based on code tree: successively bit by bit recognizing, moving down the tree using $L$ distinct paths (when $L$ > 1). The word 'successive' is key because $u_1^{(i)}$ can be hard-decoded only when $u_1^{(i-1)}$ and previous ones are known in advance.

> **Source:** https://arxiv.org/pdf/1411.7282

----------------------------------------------------------------------------------------------------------------------------------------------------

## Goal

Prediction of error numbers for a codeword coming to the decoder from an AWGN channel.
Though the decoding algorithm takes only $O(N*logN)$ operations, proactive reaction allows to de. Moreover, according to the 5G/6G requirements

----------------------------------------------------------------------------------------------------------------------------------------------------

## Data analysis & Machine Learning

> **Source:** https://www.kaggle.com/datasets/furkanercan88/5g-control-channel-transmission-dataset/data

**Samples**: 100000

**Features**: 512

**Target**: 1

### Features
Each feature represents a LLR of a polarized channel. As we have 512 features, the codeword vector length is 512 bits. Each bit is processed individually in separated polarized channels $W^{(i)}_{512}$.
### The definition of LLR

The polar code decoder is a soft-decision decoder, so it accounts both bit threshold itself and likelihood of a bit. Given that +1 symbol corresponds to bit '0', -1 symbol corresponds to '1'.

<img width="310" height="91" alt="Screenshot from 2026-09-07 20-38-40" src="https://github.com/user-attachments/assets/fd23e8af-3237-4f6e-ab53-3413b7712be0" />

1. If LLR < 0, the bit coming is likely to be '1'.
2. If LLR > 0, the bit coming is likely to be '0'.
3. The absolute value |LLR| reflects the measure of "confidence" or, more precisely, likelihood of a bit.

### LLR for normal distribution (AWGN channel)

<img width="203" height="76" alt="Screenshot from 2026-09-07 20-42-26" src="https://github.com/user-attachments/assets/99e6cff6-6aec-4bb1-81f5-bda01b4df038" />

Thus, LLR for a channel is inversely proportional to channel variance. Sometimes work with variance is more convenient: it's always a positive number. Though the information about a bit is lost, the absolute value completely reflects the likelihood of a bit whatever it was. It's a variance that will be used furthermore to analyze the dataset.

### Target

Represents the number of error bits for a sample.

### Hypothesis

1. The presence of error numbers can be related to the high variance value, more precisely, to how many times the high variance values appear in a sample.
2. The more low-LLR values in a sample, the higher a number of error.
