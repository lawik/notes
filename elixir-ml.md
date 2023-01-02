Elixir ML projects to follow:

- Nx (has sub-projects)
- Axon (has sub-projects)
- Explorer
- Bumblebee
- Evision (OpenCV bindings)

Nx and Axon make the whole ML under Elixir possible with compiler backends and building out all the primitives and whatnot. Not a thing I understand very well in the details.

Bumblebee makes particular model implementations available so that they can be used by just pulling pre-trained weights and executing them which really lowers the barrier to getting going with this stuff.

# Why do this at all?
Machine Learning is number crunching. The Erlang VM is bad at number crunching. It is not what it is for.

Machine Learning is typically done in Python. Why? High-level abstractions and convenience. Python is bad at number crunching. It is not what it is for ;)

There are Python-oriented frameworks that aim to provide performance and scalability for ML applications that literally implement the Actor model to solve the orchestration of ML tasks. Such as [Ray.io](https://docs.ray.io/en/latest/ray-core/actors.html). That's interesting.

Elixir/Erlang should not run the ML workload. It needs to compile to something faster and with Nx it does.

Fundamentally the high-level language is there to provide good APIs that can be boiled down to highly performant CPU/GPU code. That's what you want in Python as well as Nx. Here the FP approach is helpful in that it eliminates certain classes of footguns with the focus on immutability.

Python being heavily used for this is a happy accident of Python's legacy as a teaching language and it's approachability. Easy to learn. High-level, dynamic. Overall I would say the forward edge of developer experience has run away from Python though so maybe we can get something better that also better constrains the calculations that need to be constrained.

And maybe we can choose a runtime that will let us efficiently orchestrate the code for both mangling data, training and later inference. So many systems today essentially need to ship their questions to a Python-blob over some interface because they aren't built in Python but the models always are.