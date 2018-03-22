---
layout: post
title: Real-time Bilateral Filtering
excerpt_separator: ---
---

Many users have become accustomed to reducing wrinkles, freckles, and various blemishes from human subjects for a more visually appealing image or video. One effective way to solve this problem is to apply an edge-preserving filtering called a ***bilateral filter***. However, a vanilla bilateral filter has a high computational cost necessitating a powerful CPU and GPU to process full HD images in real-time. So I had been looking for an efficient alternative and finnaly found ***recursive bilateral filtering***


For more details of the algorithm, please refer to the original paper

    @inproceedings{yang2012recursive,
        title={Recursive bilateral filtering},
        author={Yang, Qingxiong},
        booktitle={European Conference on Computer Vision},
        pages={399--413},
        year={2012},
        organization={Springer}
    }
    

  > Yang, Qingxiong.
  **Recursive bilateral filtering**. 
  *European Conference on Computer Vision* 2012.

which can achieve a good trade-off:

<table>
<tr>
<td><img src="https://cloud.githubusercontent.com/assets/2270240/26041579/7d7c034e-3960-11e7-9549-912685043e39.jpg" width="300px"><br/><p align="center">Original Image</p></td>
<td><img src="https://cloud.githubusercontent.com/assets/2270240/26041586/8b4afb42-3960-11e7-9bd8-62bbb924f1e9.jpg" width="300px"><br/><p align="center">OpenCV's BF (896ms)</p></td>
<td><img src="https://cloud.githubusercontent.com/assets/2270240/26041590/8d08c16c-3960-11e7-8a0c-95a77d6d9085.jpg" width="300px"><br/><p align="center">RecursiveBF (18ms)</p></td>
</tr>
<tr>
<td></td>
<td><img src="https://cloud.githubusercontent.com/assets/2270240/26041583/86ea7b22-3960-11e7-8ded-5109b76966ca.jpg" width="300px"><br/><p align="center">Gaussian Blur</p></td>
<td><img src="https://cloud.githubusercontent.com/assets/2270240/26041584/88dfc9b4-3960-11e7-8c9d-2634eac098d0.jpg" width="300px"><br/><p align="center">Median Blur</p></td>
</tr></table>

I also made a [lightweight C++ library](https://github.com/ufoym/RecursiveBF) for recursive bilateral filtering.

---

Recursive bilateral filtering (developed by Qingxiong Yang) is pretty fast compared with most edge-preserving filtering methods
- computational complexity is linear in both input size and dimensionality:
- takes about 43 ms to process a one megapixel color image (i7 1.8GHz & 4GB mem)
- about 18x faster than *Fast high-dimensional filtering using the permutohedral lattice*
- about 86x faster than *Gaussian kd-trees for fast high-dimensional filtering*

