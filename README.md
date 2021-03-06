# Discriminative learning rates PyTorch
Adaptation of discriminative learning rates from the Fastai library for standard PyTorch.

This is an adaptation of the functions from the fastai library to be used with standard PyTorch. Please see the [fastai github](https://github.com/fastai/fastai/blob/master/fastai/train.py#L15) for original implementation.

#### Example of use

```python
#Discriminative learning rates using resnet50 with SGD and CyclicLR

model = torchvision.models.detection.maskrcnn_resnet50_fpn(pretrained=True)
params, lr_arr, _ = discriminative_lr_params(model, slice(1e-5, 1e-3))
optim = torch.optim.SGD(params, lr=1e-3, momentum=0.9, weight_decay=1e-1)
lr_scheduler = torch.optim.lr_scheduler.CyclicLR(optim, base_lr=list(lr_arr), max_lr=list(lr_arr*100))
```

* Using `slice(min_lr, max_lr)` the last layer will have max_lr as a learning rate and the first one will have min_lr. All middle layers will have learning rates logarithmically interpolated ranging from min_lr to max_lr.
* Using `sclice(lr)` the last layer will have the corresponding learning rate and all other ones will have lr/10.

We can show the learning rates using `optim.state_dict()`

| Parameter        | Lr*         
| ------------- |:-------------:|
| Group 0     | 1e-05 |
| Group 1      | 1.06512-05      |
| Group 2 | 1.13447e-05      |
| Group 3 | 1.2085e-05 |
|...  | ... | 
| Group 70 | 0.00083
| Group 71 | 0.00088
| Group 72 | 0.00094
| Group 73 | 0.00099
  
\* Learning rates are rounded here.

#### Difference from the fastai version

The big difference is that we give one independant learning rate for each layer here. In the fastai implementation layers are regrouped in blocks.
