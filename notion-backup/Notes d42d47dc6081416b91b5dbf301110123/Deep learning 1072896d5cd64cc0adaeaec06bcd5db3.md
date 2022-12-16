# Deep learning

- Architectures
    - Transformers = positional encoding + attention + self-attention
        - Used by BERT, GPT, T5, etc.
        - Originally for translation
        - TODO What is self-attention?
        - Resources
            - Paper: [[1706.03762] Attention Is All You Need](https://arxiv.org/abs/1706.03762)
            - Google talk intuition: [Transformers, explained: Understand the model behind GPT, BERT, and T5 - YouTube](https://www.youtube.com/watch?v=SZorAJ4I-sA)
    - Attention
        - Originally for translation
        - Resources
            - Andrew Ng intuition: [https://www.youtube.com/watch?v=ZU12u6-ewP0](https://www.youtube.com/watch?v=ZU12u6-ewP0)
    - Encoder-decoder
        - 2-phase: first encode into a fixed vector representation of the entire text, then decode into a new sentence.
            
            ```bash
            Input: "The cat sat on the mat"
                         _________________________
                        |                         |
                        |                         v
               Encoder ----> Fixed-length vector representation
                        |                         |
                        |_________________________|
                                                
                  Output: "Le chat était assis sur le tapis"
                         _________________________
                        |                         |
                        |                         v
               Decoder ----> Translation: "Le chat était assis sur le tapis"
            ```
            
        - Mostly limited by memory.
        - Similar to autoencoders, but autoencoders for unsupervised, encode-decoder for supervised
    - Autoencoder
        - An encoder-decoder that tries to decode back to the original input to the encoder. Thus for unsupervised / unlabeled learning.
            
            ```bash
            Autoencoder:
                  Input: "The cat sat on the mat"
                         _________________________
                        |                         |
                        |                         v
               Encoder ----> Fixed-length vector representation
                        |                         |
                        |_________________________|
                        |                         |
                        |                         v
               Decoder ----> Reconstruction: "The cat sat on the mat"
            
                  Encoder-decoder:
                  Input: "The cat sat on the mat"
                         _________________________
                        |                         |
                        |                         v
               Encoder ----> Fixed-length vector representation
                        |                         |
                        |_________________________|
                        |                         |
                        |                         v
               Decoder ----> Translation: "Le chat était assis sur le tapis"
            ```
            
    - GAN
        - Generator network and discriminator network in competition
    - RNNs have to consume all input before producing output, so are limited by ability to memorize things
- Implementations
    - ChatGPT
        - [Everything We Know About ChatGPT So Far - by swyx](https://lspace.swyx.io/p/everything-we-know-about-chatgpt)
    - Stability Diffusion by Stability AI
    - GPT-Neo, GPT-J by EleutherAI
- Experiments with language models and computing
    - [Nat Friedman on Twitter: "I’ve been playing with using GPT-3 to control a browser the last couple days. Here’s a quick demo. As you can see it's pretty neat! But also quite flakey. Will publish the source code shortly for others to try and improve. https://t.co/vXSIBsM7Rp" / Twitter](https://twitter.com/natfriedman/status/1575631194032549888)
        - https://github.com/nat/natbot
    - [Sharif Shameem on Twitter: "I gave GPT-3 access to Chrome with the objective "please buy me Airpods". Pretty interesting if you ask me 🤔 https://t.co/zwHPLZOlUJ" / Twitter](https://twitter.com/sharifshameem/status/1405462642936799247)
- Practice
    - Something like GPT-NeoX (20B) requires ~40GB for just weights, ~45GB for inference—no single consumer GPU for this, so need (say) 2x 3090s ([source](https://www.youtube.com/watch?v=bAY85Om5O6A))
- Bibliography
    - BERT: [[1810.04805] BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding](https://arxiv.org/abs/1810.04805)
    - GPT3: [[2005.14165] Language Models are Few-Shot Learners](https://arxiv.org/abs/2005.14165)
    - ??: [[2205.11916] Large Language Models are Zero-Shot Reasoners](https://arxiv.org/abs/2205.11916)
    - Transformers: [[1706.03762] Attention Is All You Need](https://arxiv.org/abs/1706.03762)
- TODO understand definitions
    - seq2seq
    - bidirectional lstm
    - embed strings
    - lstm that attends over the input string as well as conditioned on the input statement