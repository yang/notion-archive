# Deep learning and software engineering

- Learning materials
    - Survey of neural program synthesis: [Advanced Machine Learning Day 3: Neural Program Synthesis - YouTube](https://www.youtube.com/watch?v=nlgA2gnwscQ&list=WL&index=8)
    - MIT Lectures: [Introduction to Program Synthesis](https://people.csail.mit.edu/asolar/SynthesisCourse/index.htm)
    - IISC Lectures: [Program Synthesis meets Machine Learning](https://www.csa.iisc.ac.in/~deepakd/psml-2020/)
    - CVPR workshops: [Neuro-Symbolic Visual Reasoning and Program Synthesis](http://nscv.csail.mit.edu/)
- Ways to bridge ML and program synthesis
    - Pure symbolic search, e.g. FlashFill—problem is exponential, must craft a DSL that balances expressivity and concision (for practically reducing search time)
    - Symbolic search guided by neural network that ranks, at each node in the AST, what are most likely. Truncate least likely. Explore several nodes breadth first to avoid dead ends. (Is this what RobustFill is?)
    - Pure neural search. E.g. the SQL work. Treat as machine translation. Use encoder-decoder with attention.
        - To guarantee programs that at least compile, can (a) tweak the standard beam search that generates the final output to also discard invalid strings-so-far, and (b) tweak the neural search itself to only consider, at each step, the valid next tokens [did not understand how this works].
    - Model programs as not just ASTs (which is an improvement over linear sequence of tokens), but as graphs that capture semantic information, such as what’s observed by compilers computing dataflow dependencies (also includes the linear sequence chain and AST tree)
- Opportunities
    - Editing large code
    - Learning to use libraries empirically
    - Learning codebases with dynamic techniques
    - Generating domain specific representations multi-modally
- TODO Unsorted
    - [DreamCoder: Growing generalizable, interpretable knowledge with wake-sleep Bayesian program learning - YouTube](https://www.youtube.com/watch?v=qtu0aSTDE2I)
        - Alternates between sleep and wake phases
        - Combines multiple techniques—symbolic search, neural search, and PGM
    - [AI Coding with CodeRL: Toward Mastering Program Synthesis with Deep Reinforcement Learning](https://blog.salesforceairesearch.com/coderl/)
        - [code](https://github.com/salesforce/CodeRL)
- Spreadsheet applications
    - [FlashFill](https://blog.sigplan.org/2021/09/14/the-story-of-the-flash-fill-feature-in-excel/)
    - RobustFill: [Deep Learning for Program Synthesis - Microsoft Research](https://www.microsoft.com/en-us/research/blog/deep-learning-program-synthesis/)
    - SpreadsheetCoder: [https://arxiv.org/abs/2106.15339](https://arxiv.org/abs/2106.15339)
- Generating code from visual representations
    - [pix2code from uizard](https://uizard.io/blog/pix2code/) - paper with code
    - [Sketching Interfaces – Airbnb Design](https://airbnb.design/sketching-interfaces/) - only a video and blog post
    - pix2struct: [[2210.03347] Pix2Struct: Screenshot Parsing as Pretraining for Visual Language Understanding](https://arxiv.org/abs/2210.03347)
    - [Understanding HTML with Large Language Models | Hacker News](https://news.ycombinator.com/item?id=33168043)
- Generating visual designs from natural language
    - [GPT3 demo](https://twitter.com/jsngr/status/1284511080715362304?ref_src=twsrc%5Etfw) that later feeds into [Magician](https://www.figma.com/community/plugin/1151890004010191690/Magician)
- Competitive coding
    - [DeepCoder: Learning to Write Programs - Microsoft Research](https://www.microsoft.com/en-us/research/publication/deepcoder-learning-write-programs/)
    - [Competitive programming with AlphaCode](https://www.deepmind.com/blog/competitive-programming-with-alphacode)
- Understanding/modeling code
    - Polycoder: LLM trained only code exclusively, outperforms general LLMs ([paper](https://arxiv.org/abs/2202.13169), [code](https://github.com/VHellendoorn/Code-LMs))
    - [[1910.00577] Structural Language Models of Code](https://arxiv.org/abs/1910.00577) instead of flat sequences
    - [code2vec](https://code2vec.org/): code → AST → vector
        
        ![Untitled](Deep%20learning%20and%20software%20engineering%2045a9dd27343b41ba8f5f7999d1975db9/Untitled.png)
        
- Bibliography
    - [[2007.03629] Strong Generalization and Efficiency in Neural Programs](https://arxiv.org/abs/2007.03629)
    - [[2207.11765] Neurosymbolic Repair for Low-Code Formula Languages](https://arxiv.org/abs/2207.11765)
    - [[2108.07732] Program Synthesis with Large Language Models](https://arxiv.org/abs/2108.07732)
    - [State of Deep Learning for Code Generation (DL4Code) | Victor Dibia](https://victordibia.com/blog/deep-learning-code-generation/)
    - [DeepCoder: Learning to Write Programs - Microsoft Research](https://www.microsoft.com/en-us/research/publication/deepcoder-learning-write-programs/)
    - [Competitive programming with AlphaCode](https://www.deepmind.com/blog/competitive-programming-with-alphacode)
    - [OpenAI Codex](https://openai.com/blog/openai-codex/)
    - [[2002.08155] CodeBERT: A Pre-Trained Model for Programming and Natural Languages](https://arxiv.org/abs/2002.08155)
    - What is this…. [[2109.00859] CodeT5: Identifier-aware Unified Pre-trained Encoder-Decoder Models for Code Understanding and Generation](https://arxiv.org/abs/2109.00859)
    - [Conversational AI Programming with CodeGen: Let AI Write Code For You](https://blog.salesforceairesearch.com/codegen/)
    - [[2107.03374] Evaluating Large Language Models Trained on Code](https://arxiv.org/abs/2107.03374)
- Example applications
    - The Figma prototype: [I build my ideas #8 - 07/19/20](https://ibuildmyideas.substack.com/p/i-build-my-ideas-8-071920)
        
        ![Untitled](Deep%20learning%20and%20software%20engineering%2045a9dd27343b41ba8f5f7999d1975db9/Untitled%201.png)
        
    
    ```
    {
        "navigation": [
            {"icon": "camera"},
            {"title: "Photos"},
            {"icon": "message"}
        ],
        "photos": [
            {"icon": "user"},
            {"frame": "photo"},
            {"icon": "heart"},
            {"icon": "chat bubble"}
        ]
    }
    ```
    
    - React Tailwind code:
    
    [https://twitter.com/gabe_ragland/status/1598068207994429441](https://twitter.com/gabe_ragland/status/1598068207994429441)
    
- Generic prompts
    - Show me just the source code, with no prose or explanation, for only the main listing page of an Airbnb clone written in React, Next.js, Typescript, and Firebase. Support the ability to search, search on a map, filter, and add favorites.
- Implementations