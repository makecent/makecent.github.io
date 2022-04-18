---
title: Pytorch Practices
---

# Useful functions
``` python
# batch-wise matrice multiplication
torch.bmm(A, B), A.bmn(B) 

# buffer frequently reused values (non-parameters) for speed up, e.g., positional embeddings
nn.Module.register_buffer(name, tensor, persistent=True) 

# Returns the upper/lower triangular part of the matrix, the other elements are set to 0.
torch.triu(A), A.triu()
torch.tril(A), A.tril()

# Directly return the max values along the specific dimension. No need of using `max_v, _ = A.max(dim=1)`
A.max(dim=1).values, A.max(dim=1).indices 

# Get the topk (top-5 here) values and indices. `.values` and `.indices` can be used to access the specific part.
A.topk(5)

# Matrix multiplication on the last two dimensions of tensor A and B. Multiplication (with Broadcasting) on the remaining dimensions.
A @ B 

# Casting tensor to target dtype
A.double(), A.float(), A.long(), A.int(), A.bool()

# Convert A to the type of B, including `dtype` and `device`.
A.type_as(B) 

# Get zero/one tensor like a specific tensor
A.new_zeros(size, dtype=None, device=None, requires_grad=False), A.new_ones(...)
torch.zeros_like(A), torch.ones_like(A)   # including size

# Apply function
A.apply_(func)
A.map_(B, func)   # B as argument
```
gogo
