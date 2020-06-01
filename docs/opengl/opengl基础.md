# opengl 基础

#### 空间变换和矩阵
```plantuml

[*] -r-> 局部空间
局部空间 -r-> 世界空间 :模型矩阵(Model Matrix)
世界空间 -d-> 观察空间 :观察矩阵(View Matrix)
观察空间 -d-> 裁剪空间 :投影矩阵(Projection Matrix)
裁剪空间 -l-> 屏幕空间

```
* 投影
 正射投影：裁剪空间为长方体，投影远近一样。
 透视投影：裁剪空间为梯形平截头体，投影远小近大。

#### 纹理

```plantuml

[*] -r-> 生成纹理
生成纹理 -r-> 设置纹理过滤和环绕方式
设置纹理过滤和环绕方式 -d-> 加载纹理数据
加载纹理数据 -d-> 激活纹理单元并设置采样器
激活纹理单元并设置采样器 -l-> 应用纹理

```

* 生成纹理
```java
int[] textureHandle = new int[1];
GLES20.glGenTextures(1, textureHandle, 0);
```
* 设置纹理过滤和环绕方式
```java
GLES20.glBindTexture(textureTarget, textureHandle[0]);
GLES20.glTexParameteri(textureTarget, GLES20.GL_TEXTURE_MIN_FILTER, minFilter);
GLES20.glTexParameteri(textureTarget, GLES20.GL_TEXTURE_MAG_FILTER, magFilter); //线性插值
GLES20.glTexParameteri(textureTarget, GLES20.GL_TEXTURE_WRAP_S, wrapS);
GLES20.glTexParameteri(textureTarget, GLES20.GL_TEXTURE_WRAP_T, wrapT);
```
* 加载纹理数据
```java
GLES20.glBindTexture(GLES20.GL_TEXTURE_2D, textureId);
GLES20.glTexImage2D(GLES20.GL_TEXTURE_2D, 0,
                GLES20.GL_RGBA, videoW, videoH, 0,
                GLES20.GL_RGBA, GLES20.GL_UNSIGNED_BYTE, colorByteBuffer);
```
* 激活纹理单元并设置采样器
```java
// 激活纹理单元0
GLES20.glActiveTexture(GLES20.GL_TEXTURE0);
// 绑定2D纹理
GLES20.glBindTexture(target, textureId);
// 设置纹理单元的采样器
GLES20.glUniform1i(mTextureLoc, 0);
```
* 应用纹理
```java
GLES20.glDrawArrays(mDrawMode, 0, mGLCoordBuffer.getVertexCount());
```

#### 混合
`C¯result=C¯source∗Fsource+C¯destination∗Fdestination`
先画的是`dst`，后画的是`src`
**步骤：**
```java
// 开启混合模式
GLES20.glEnable(GLES20.GL_BLEND);
// 设置混合src 和 dst 混合因子
GLES20.glBlendFuncSeparate(GLES20.GL_SRC_ALPHA,
        GLES20.GL_ONE_MINUS_SRC_ALPHA, GLES20.GL_ONE, GLES20.GL_ONE);
// 设置混合方程式
GLES20.glBlendEquation(GLES20.GL_FUNC_ADD);
// 画
// 关闭混合模式
GLES20.glDisable(GLES20.GL_BLEND);
```

#### 帧缓冲

* 渲染缓冲对象附件
* 颜色附件(纹理)

## 编码
* MediaCodec，MediaMuxer，Surface
* 构建 EGLDisplay，EGLContext，EGLConfig
* 编码器，获取 Surface，转换成 EGLSurface，切换到该 EGLSurface
* 绘制
* EGLDisplay，EGLSurface写入时间戳
* eglSwapBuffers，前后台数据交换一下
* 开启编码，MediaCodec 拿出后台数据，编码，MediaMuxer 写到本地。
