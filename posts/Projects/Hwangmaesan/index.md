---
layout: page
title: 황매산 등산경로 분석
---
{% assign dirs = page.dir | split: "/" %}
{% assign category = dirs[-1] %}

## 개요

---

- &nbsp;프로젝트 카테고리에 넣어도 될 지 고민했지만 프로그래밍으로 처음 의미있는 일을 한 것이라 기록으로 남겨둡니다!
- &nbsp;2017년 7월경 조경학과 환경 관련 연구실에서 대학원생과 학부생들이 같이 진행하는 파이썬 스터디를 열었습니다. 저도 참여하였습니다.

{: .p_img}

![python_book.png]({{ site.imgbase }}Maehwasan/python_book.png){: width='50%' height='50%' }{: .center}<small>당시 사용했던 책. 말 그대로 ArcGIS를 위한 python이다.</small>

- &nbsp;한편, 옆 방에 조경 설계 연구실이 있었습니다. 설계 연구실에서 황매산 관련 프로젝트를 하고 있었는데, 사람들의 등산경로를 바탕으로 설계를 진행해야 했습니다.

- &nbsp;하지만 설계 연구실은 문제를 맞닥드립니다.

  - &nbsp;등산경로를 분석하기 위해선 등산경로 파일을  세 단계 가공해야 했습니다.

  - .kml => vector => .mxd => raster

    ![process.png]({{ site.imgbase }}Maehwasan/process.png){: .center}

  - &nbsp;저희 학과에선 ArcGIS라는 공간분석 소프트웨어를 사용하는데 위 분석을 수행하기 위해 각 단계마다 파일을 선택하고, 옵션을 선택해 변환해야 했습니다. 

  - &nbsp;문제는 등산경로가 500여개라는 것이었습니다. 연구실 인력을 상당수 할애해야 했습니다.

- &nbsp;제가 환경 연구실에서 파이썬 스터디를 하고 있다는걸 알고 있던 설계 연구실 친구가 도움을 요청해왔습니다. 파이썬을 이용해서 ArcGIS를 자동화 할 수 있으니 등산경로 파일을 가공하는 걸 도와달라는 것이었습니다.



## 가공

---

- &nbsp;사실 이것을 위한 코드는 arcpy 라이브러리를 사용해 for문을 돌리는 정도로, 다 해봐야 15줄 남짓입니다.

- .kml => vector

  ```python
  import arcpy
  
  # 폴더 지정 및 파일 리스트업
  arcpy.env.workspace = "C:/kml_source"
  list = arcpy.ListFiles()
  
  # KMLToLayer 메서드로 kml파일을 벡터파일로 변환
  for kml in list:
      arcpy.conversion.KMLToLayer(kml, "C:/feature_data")
  ```

- vector => mxd

  - &nbsp;여러 벡터데이터를 Map Exchange Document 라는 데이터 형태로 합치는 과정으로, 이 부분은 폴더 째로 한번에 수행할 수 있어 수동으로 진행했습니다.

- mxd => raster

  ```python
  import arcpy
  
  # 폴더 지정 및 파일 읽어오기
  arcpy.env.workspace = "C:/mxd"
  mxd = arcpy.mapping.MapDocument("C:/mxd/feature_total.mxd")
  
  # 벡터 파일안의 여러 feature중 Polyline만 뽑아냄
  df = arcpy.mapping.ListDataFrames(mxd)[0]
  list = arcpy.mapping.ListLayers(df, "Polylines", df)
  
  # Polyline을 raster 데이터로 변환
  for pl in list:
      arcpy.conversion.FeatureToRaster(pl, "OID", pl.longName[0:13])
      print pl.longName[0:13]
  ```

  



## 결과

---

- &nbsp;가공한 데이터를 넘겨줬고, 며칠 후 고맙다는 말과 함께 결과물을 보여주었습니다.

![analysis.jpg]({{ site.imgbase }}Maehwasan/analysis.jpg){: width='75%' }{: .center}

- &nbsp;등산경로들을 중첩한 것으로, 빨간색일수록 등산객들이 많이 이용한 등산경로입니다. 
- &nbsp;하단의 4개는 순서대로 봄, 여름, 가을, 겨울 별 등산경로입니다.

![analysis2.jpg]({{ site.imgbase }}Maehwasan/analysis2.jpg){: width='75%' }{: .center}

- &nbsp;실제 황매산 위에 등산경로 데이터를 올린 결과입니다.

- &nbsp;분석 결과는 요긴하게 쓰였다고 합니다. 사실 프로그래밍 자체는 정말 별거 아니었지만, 이 정도만 해도 정말 많은 노동력을 절감해준걸 보고 프로그래밍 배우길 잘했다고 생각했습니다.
