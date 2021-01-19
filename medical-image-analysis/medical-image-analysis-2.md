# medical image analysis 2

취득된 영상을 판독하는 데에 사용 

Prevent blindness 

-&gt; 조기 치료 

Classify cancer, identify mutations 

Promote patient safety

Predict depth 

reconstruction engine 분야에서도 dL이 사용됨 

영상을 디지털화 시키는 부분

CheXNet 확인 



problem -&gt; Rule -&gt; Evaluation -&gt; error analysis -&gt; problem -&gt; rule -&gt; evaluation -&gt; launching



-&gt; Rules에 ML 넣음 

solution을 분석하는 연구 -&gt; discovery =&gt; data mining 

### Classification

개 vs 고양이 분류 

computer vision 문제를 ML 모델로 학습 시킴 

Normal vs Disease

<table>
  <thead>
    <tr>
      <th style="text-align:left"></th>
      <th style="text-align:left">Conventional method</th>
      <th style="text-align:left">Deep Learning method</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Classification</td>
      <td style="text-align:left">
        <p>Logistic regression</p>
        <p>Neural network</p>
        <p>Support Vector Machine</p>
        <p>Random forest</p>
      </td>
      <td style="text-align:left">
        <p>Deep neural network</p>
        <p>Convolutional neural network
          <br />
        </p>
        <p></p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Segmentation</td>
      <td style="text-align:left">
        <p>Thresholding</p>
        <p>Region growing</p>
        <p>Graph cut</p>
        <p>Active contour model</p>
        <p>Active shape model</p>
      </td>
      <td style="text-align:left">
        <p>FCN</p>
        <p>U-Net</p>
        <p>DeepLab</p>
        <p></p>
        <p></p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Enhancement</td>
      <td style="text-align:left">
        <p>Normalization</p>
        <p>Histogram equalization</p>
        <p>Filtering</p>
        <p>Dictionary learning</p>
      </td>
      <td style="text-align:left">
        <p>SRCNN</p>
        <p>GAN</p>
        <p>SRGAN</p>
        <p></p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Registration</td>
      <td style="text-align:left">
        <p>Transformation matrix</p>
        <p>Iterative closet point(ICP)</p>
        <p>Non rigid ICP</p>
        <p>Deformable models</p>
      </td>
      <td style="text-align:left">
        <p>FlowNet</p>
        <p>CNN for Registration</p>
        <p></p>
        <p></p>
      </td>
    </tr>
  </tbody>
</table>



### segmentation

자율주행 -&gt; segmentation 많이 쓰임 

medical에도 사용 가능 

### Enhancement

해상도 낮 -&gt;높 

### Registration 

특정 영상을 정합 



