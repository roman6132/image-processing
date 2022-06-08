## Лабораторная работа 4. Повышение резкости изображений.

План работ:
1. Скачать любое цифровое изображение. Желательно многоцветное

    Исходное изображение:
    
<img src="resources/picture.jpg" width="500"/>

2. Применить к исходному изображению Гауссово размытие. Отобразить результат.


```
 public BufferedImage gaussian(BufferedImage picture) throws IOException {
        BufferedImage result = new GaussBlur().process(picture);
        save(result, "result/gauss", "result", FORMAT);
        return result;
    }
```
[GaussBlur](resources/GaussBlur.java)

  
<img src="resources/gauss/result.jpg" width="500"/>

4. Реализовать функцию повышения резкости методом усиления границ.
5. Применить построенную функцию к размытому изображению. Вывести результат работы функции вместе с исходным изображением.
  Данная функция применяет Гауссово размытие повторно и вычитает результат из оригинального изображения. Таким образом получается "маска" с усиленными границами, которая прибавляется к исходному изображение. 
  
```
  public BufferedImage blur(BufferedImage picture, int steps) throws IOException {
        BufferedImage result = new BufferedImage(picture.getWidth(), picture.getHeight(), TYPE_INT_RGB);
        GaussBlur gblur = new GaussBlur();
        for (int i = 0; i < steps; i++) {
            result = sum(picture, diff(picture, gblur.process(picture)));
            picture.setData(result.getData());
            save(result, "result/sharp", "result" + i, FORMAT);
        }
        return result;
    }
      
                                 
```

Step1 | Step2 | Step3 | Step4 | Step5
------ | ------|----------| ------|----------
<img src="resources/sharp/result0.jpg" width="500"/>    |  <img src="resources/sharp/result0.jpg" width="500"/>   |  <img src="resources/sharp/result2.jpg" width="500"/> | <img src="resources/sharp/result3.jpg" width="500"/> | <img src="resources/sharp/result4.jpg" width="500"/>
                                
6. Используя две любые функции повышающие резкость изображения, обработать размытое изображение. Результаты также вывести вместе с исходным изображением для сравнения.
  * Первая функция 
  
   Здесь используется размытие по Гауссу, но вычитание размытой версии из исходного изображения происходит взвешенным образом.
  
```
    private void weighted(BufferedImage picture) throws IOException {
        Mat blurredOpenCV = new Mat();
        Imgproc.GaussianBlur(img2Mat(picture), blurredOpenCV, new Size(0, 0), 3);
        Mat weightedOpenCV = blurredOpenCV.clone();
        Core.addWeighted(blurredOpenCV, 1.5, weightedOpenCV, -0.5, 0, weightedOpenCV);
        BufferedImage result = (BufferedImage) HighGui.toBufferedImage(weightedOpenCV);
        save(result, "result/weighted", "result", FORMAT);
    }
  
```
  
<img src="resources/weighted/result.jpg" width="500"/>
  
  * Вторая функция 
  
  В этом способе повышения резкости ипользуется функция 2D-фильтрации и сверточная матрица, часто называемая ядром. 

```
  
   private void convMatr(BufferedImage picture) throws IOException {
        Mat convMatrOpenCV = new Mat(3, 3, CvType.CV_16SC1);
        convMatrOpenCV.put(0, 0, 0, -1, 0, -1, 5, -1, 0, -1, 0);
        Mat sharpedOpenCV = new Mat();
        Imgproc.filter2D(img2Mat(picture), sharpedOpenCV, -1, convMatrOpenCV);
        BufferedImage result = (BufferedImage) HighGui.toBufferedImage(sharpedOpenCV);
        save(result, "result/convMatr", "result", FORMAT);
    }
  
```
  
<img src="resources/kernel/result.jpg" width="500"/>
