Useful functions

`torch.bmm` (Tensor.\*): batch-wise matrice multiplication.

`nn.Module.register_buffer(name, tensor, persistent=True)`: buffer frequently reused values (non-parameters) for speed up.[link.](https://pytorch.org/docs/stable/generated/torch.nn.Module.html#torch.nn.Module.register_buffer)

`torch.triu` and `torch.tril` (Tensor.\*): Returns the upper/lower triangular part of the matrix, the other elements are set to 0. [link.](https://pytorch.org/docs/stable/generated/torch.triu.html?highlight=triu#torch.triu)

`Tensor.max(dim=1).values`: Directly return the max values along the specific dimension. No need of using `max_v, _ = Tensor.max(dim=1)`. Similary, `Tensor.max(dim=1).indices` directly return the max indices.

`Tensor.topk(5)`: Get the topk (top-5 here) values and indices. Subsequent `.values` and `.indices` can be used to access the specific part.
