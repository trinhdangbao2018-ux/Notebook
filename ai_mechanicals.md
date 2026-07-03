Sign:
T = Number of tokens in the current input/context
V = Vocabulary size
d = Dimension of each token vector / hidden state
L = Number of Transformer blocks/layers
Note:
For simplicity, this explanation ignores the batch dimension.
In real implementations, shapes are often [B, T, d], where B is batch size.

An LLM model go through these following step:

    1, User input:
        Format: Pure text, numbers, files, etc

    2, Tokenize:
        Input: Take the user input
        Process: From the user input, change it to ids
        Output: Each token was map text (shaped T)
        Reason: To label each token with an different IDs for AI to later mark them and change them to vector to process with

    3, Embedding:
        Input: Labeled token 
            (input shape of this step is pure T)
        Process: Change token to vector form, usually a lot of dimension. 
            (By Using an Dictionary shaped (V,d) to look up for the words and turn them into an vector)
        Output: Vectorized token 
            (Exp: Embedding matrix E have a shave of [T,d])
        Reason: Reason: By turning tokens into vectors, the model can process them with neural network operations. Later, after the Transformer blocks, the model projects the final hidden vector back into vocabulary space to predict the next token.

    4, Adding Position (Positional Embedding/ Positional Encoding/ RoPE):
        Input: Vectorized token
            (input shape is [T,d])
        Process: It injects position information into the existing vector representation.
        Output: Mark the word position in a sentence, paragraph, and file
            (output shape is still [T,d])
        Reason: For the AI to know what the index of the words, with the context of the words, AI can understand better about the context and get a bigger picture. Without this step "Human eat dogs"  and "Dog eat human" are basically the same

    5, Transformer blockS (The most important step):
        Input: Vectorize token
            (input shape is [T,d])
        Process: Go through a lot of layer
            (output shape is [T,d])
        Each Transformer block usually contains:
            LayerNorm
            Self-attention
            Residual connection
            MLP / Feed-forward network
            Residual connection
            Output: More context vector
        Reason: To add a context to a vector

    6, Output projection / language modeling head:
        Input: Final hidden states from the Transformer stack
            (input shape is T,d)
        Process: The language modeling head projects the hidden vector into vocabulary space.
        Output: Logits with shape [V].
        Reason: The model needs to assign a raw score to every possible next token in the vocabulary.

    7, Next-token probabilities/ Softmax:
        Input: Logits with shape [V].
        Process: Softmax converts raw logits into probabilities.
        Output: A probability distribution over the vocabulary with shape [V].
        Reason: The model now has a probability for each possible next token.

    8, Decoding strategy:
        Input: Next-token probabilities with shape [V].
        Process: A decoding strategy selects one token from the probability distribution.
        Common decoding strategies:
        Greedy decoding
        Sampling
        Top-k sampling
        Top-p / nucleus sampling
        Temperature-based sampling
        Beam search
        Output: One selected token ID.
        Reason: The model produces probabilities, but the system needs to choose one actual token to generate next.

    9, Append token:
        Input: Previous token sequence and the selected next token ID.
        Process: The selected token is appended to the current sequence.
        Output: A longer token sequence.
        Reason: Autoregressive language models generate text one token at a time. The newly generated token becomes part of the context for the next prediction.

    /*Loop again and again from token embedding to append token until the answer is generated*/

    10, Tokenizer decode:
        Input: Generated token IDs.
        Process: The tokenizer maps token IDs back into human-readable text.
        Output: Final text answer.
        Reason: The model generates token IDs, but users need normal text.


In older model: Adding position is outside transformer block
https://bbycroft.net/llm?utm_source=chatgpt.com