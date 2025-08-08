# Edge Detection Example
### Verticale edge detection
![](https://i.imgur.com/5nsVIqc.png)

![](https://i.imgur.com/cevDjNi.png)

![](https://i.imgur.com/O18ec2r.png)
why this is for vertical edge detection
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20250807203103.png]]

![[Pasted image 20250807203118.png]]
the wight block in the middle means strong vertical edge in the middle

But what if ur image is darker in the right and whiter in the left?
![](https://i.imgur.com/h58qDj6.png)
no u get -30's instead of 30

### Vertical Vs horizontal

![[Pasted image 20250807203818.png]]

![[Pasted image 20250807203936.png]]

### Learning to detect edges
the sobel filter, schorr filter
![](https://i.imgur.com/463Rny2.png)

u can also treat those numbers as learning params using backprob
![](https://i.imgur.com/hQRv0Lb.png)

---
# Padding
Note:
if u have n,n image matrix and the filter is f,f matrix then the output will be n-f+1, n-f+1 matrix

The downside of the prev method is that every time u apply this method ur image shrinks in every time. and the second downside is that some pixels are used only ones while others or used many times so ur dropping a lot of information.
We solve this by **Padding**. 
![](https://i.imgur.com/Ow1BBlP.png)
so now instead of 6,6 image u have 8,8 image and by applying the filter u will get 6,6 matrix again. 

#### Valid and Same convolutions
![](https://i.imgur.com/VhZfgUX.png)

---
# Strided Convolutions
![](https://i.imgur.com/5ylpheZ.png)
step the blue boxby 2 steps
![](https://i.imgur.com/0GpnqCn.png)

![](https://i.imgur.com/DIcH0oX.png)

![](https://i.imgur.com/LTtzVe0.png)

![](https://i.imgur.com/ZiKuwg0.png)

---
# Convolutions Over Volume
![](https://i.imgur.com/Rha5Cu0.png)
![](https://i.imgur.com/KbLMnRQ.png)
Example of filter that detects edges only in the red channel 
![](https://i.imgur.com/4lzm3uY.png)
or for all color channels
![](https://i.imgur.com/njmnBwu.png)
For applying **Multiple Filters**:
![](https://i.imgur.com/MGJvctP.png)
![](https://i.imgur.com/Xw11Ygg.png)


---
# One Layer of a Convolutional Network
![](https://i.imgur.com/kPcvPXG.png)

![](https://i.imgur.com/RmKZEmm.png)

![](https://i.imgur.com/ifmvoLe.png)
