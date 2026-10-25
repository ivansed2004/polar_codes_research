# **Data analysis of LLR and Machine Learning model to predict bit error numbers in polar code decoder for AWGN channel**

## Glossarium

1. AWGN -> Additive White Gaussian Noise
2. B-DMC -> Binary-input Discrete Memoryless Channel
3. FEC -> Forward Error Correction
4. LDPC -> Low Density Parity Check
5. LLR -> Log-Likelihood Ratio
6. PBCH -> Physical Broadcast CHannel
7. PDCCH -> Physical Downlink Control CHannel
8. PUCCH -> Physical Uplink Control CHannel
9. PDSCH -> Physical Downlink Shared CHannel
6. PUSCH -> Physical Uplink Shared CHannel
8. SCL -> Successive Cancellation List
9. UE -> User Equipment

## Introduction

**Polar codes** are block codes that have been proposed by Erdal Arikan, IEEE Senior Member, in 2009 [1].
The core concept of polar codes is **CHANNEL POLARIZATION**. The Theorem of Channel Polarization [1] proves that by applying a recursive linear transformation to $N = 2^n$, for any $δ \in (0; 1)$, the synthesized B-DMC $W_N^{(i)}$ (derived from N copies of original channel $W$) split into two extremes as $N \to \infty$:
1. **Case 1**: A fraction of subchannels acquires the symmetric capacity $I(W_N^{(i)}) \to I(W)$, where $I(W_N^{(i)}) \in (1-δ; 1]$.
2. **Case 2**: The remaining subchannels acquire capacity $I(W_N^{(i)}) \to 1-I(W)$, where $I(W_N^{(i)}) \in [0; δ)$.

The definition of polar codes $(N, K, A, u_{A^{c}})$ includes the following [1]:
1. $N$ - the overall number of bits in a codeword;
2. $K$ - the number of information bits;
3. $A$ - the set of unknown bits to be decoded;
4. $u_{A^{c}}$ - the set of so called frozen bits.

Let $u_{i}$ be a **frozen bit**, a bit with the predefined value known both for a transmitter and a receiver. When declaring the polar codes, frozen bits are included in $u_{A^{c}}$. For example, for code (8,5,{3,5,6,7,8},(0,0,0)) $u_1$ = $u_2$ = $u_4$ = 0 regardless of the DE (decision element) output for the corresponding channel. It's obvious to send them using **Case-2** subchannels. The remaining bits ($u_3$, $u_5$, $u_6$, $u_7$, $u_8$) acquire the value depending on the DE output based on LLR in the **Case-1** subchannels. The channel polarization is a phenomenon that makes it possible.

## Applications & Use Cases

Polar codes are successfully applied for Control and Broadcast Channels (PDCCH, PUCCH, PBCH) [2] in 5G radio interface due to their superior performance on short payloads (up to N = 1024 bits) and SCL decoding complexity as $O(LN*logN)$ [3], while LDPC codes handle large-block Shared Channels (PDSCH, PUSCH) to reach Shannon capacity. There exist the cases of applying polar codes in optical communications.

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
Though the decoding algorithm takes only $O(N*logN)$ operations, proactive reaction allows to accelerate avoiding long-term decoding process. The goal completely adopts the 5G/6G requirements claiming **low-latency communications** (uRLLC for 5G and hRLLC for 6G).

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

Thus, LLR for a channel is inversely proportional to channel variance. Sometimes work with variance is more convenient: it's always a positive number. Though the information about a bit is lost, the absolute value completely reflects the likelihood of a bit whatever it was. It's a variance that will be used further to analyze the dataset.

### Target

Represents the number of error bits for a sample.

### Hypotheses

- **Mapping**. Using data analysis of the original dataset, some artificial mathematical object can be found that allows to correspond it to the number of error of a row (codeword).

### Research results

// Here will be results

### Conclusion

// Here will be conclusion

### Machine Learning metrics

**1. Regression model (XGBRegressor):** \
    **1.1. The first attempt:** \
        1.1.1. Train MSE: 0.1258 \
        1.1.2. Train MAE: 0.2087 \
        1.1.3. Train R^2: 0.8576 \
        1.1.4. Test MSE: 0.3744 \
        1.1.5. Test MAE: 0.3413 \
        1.1.6. Test R^2: 0.5757 \
   **1.2. The second attempt:** \
        1.2.1. Train MSE: 0.1221 \
        1.2.2. Train MAE: 0.2059 \
        1.2.3. Train R^2: 0.8617 \
        1.2.4. Test MSE: 0.3817 \
        1.2.5. Test MAE: 0.3416 \
        1.2.6. Test R^2: 0.5684 \
**2. Classification model (Multiple Additive Regression Trees, MAPT):** \
    **2.1. The first attempt:** \
        2.1.1. Train Accuracy: 0.8013 \
        2.1.2. Train Precision: 0.8157 \
        2.1.3. Train Recall: 0.8013 \
        2.1.4. Train F1-Score: 0.7167 \
        2.1.5. Test Accuracy: 0.7985 \
        2.1.6. Test Precision: 0.6764 \
        2.1.7. Test Recall: 0.7985 \
        2.1.8. Test F1-Score: 0.7099 \
    **2.2. The second attempt:** \
        2.2.1. Train Accuracy: 0.8013 \
        2.2.2. Train Precision: 0.8157 \
        2.2.3. Train Recall: 0.8013 \
        2.2.4. Train F1-Score: 0.7167 \
        2.2.5. Test Accuracy: 0.7985 \
        2.2.6. Test Precision: 0.6764 \
        2.2.7. Test Recall: 0.7985 \
        2.2.8. Test F1-Score: 0.7099
