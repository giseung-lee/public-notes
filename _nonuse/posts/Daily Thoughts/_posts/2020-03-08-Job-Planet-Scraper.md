---
layout: post
title: 잡플래닛 면접 후기 스크래퍼
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}

- 면접 준비하던 중 개인적인 용도로 사용하려 제작 했으나 혹시 쓰실 분들은 참고하셔도 좋을 것 같아 올립니다. 따로 깃헙에 올릴정도는 아니고 그냥 간단한 스크립트 수준입니다.

- 우선 필요한 모듈을 불러오고 면접 후기들을 담을 Review 클래스를 만듭니다.
  
  - BeautifulSoup은 파이썬 웹 크롤링에 많이 사용되는 라이브러리라고 합니다.
  
  ```python
  import requests
  from bs4 import BeautifulSoup
  
  class Review:
      def __init__(self, department, postdate, date, result,head,  question, answer, way, result_date):
          self.department=department
          self.postdate=postdate
          self.date=date
          self.result=result
          self.head = head
          self.question=question
          self.answer=answer
          self.way=way
          self.result_date=result_date
  ```


- 다음으로 로그인 페이지와 Id, Pw, 크롤링할 페이지, 시작~끝 페이지를 설정합니다.

  - 여기서 중요한건, Id는 잡플래닛 **면접 후기 열람이 가능한 Id여야 합니다!!**
  - BASE_URL은 크롤링 시작할 면접 후기 페이지의 1페이지 URL 입니다. (직급, 직종 등 필터링 걸어도 상관 없습니다.)

  ![crawler_url.png]({{ imgurl }}/crawler_url.png)

    ```python
    LOGIN_PAGE = "https://www.jobplanet.co.kr/users/sign_in" # 고정
    ID = 'userId' # id
    PW = 'userPw' # pw
    BASE_URL = "https://www.jobplanet.co.kr/companies/87444/interviews_by_filter?by_occupation=11600&by_job_rank=&by_success="  # 각자 원하는 회사의 면접후기 url
    BASE_URL += '&page=' # 고정
    start_page = 51 # 시작 페이지
    end_page = 104 # 끝 페이지
    ```

- 다음으로 한 페이지를 크롤링 하는 함수를 만들어 줍니다. 작성일 기준의 잡플래닛의 html에 맞게 짜여졌으므로 나중엔 작동 안할 가능성이 큽니다. 입력받은 url을 get 하고 get의 결과를 파싱하는 과정입니다.

    ```python
    def get_one_page(url):
        print(">> "+url)
        # request
        req = s.get(url)
        # parer
        bs = BeautifulSoup(req.text, 'html.parser')
        # sections
        sections = bs.find_all('section', attrs = {'class' : 'content_ty4'})
        reviews_per_page = []
        for section in sections:
            # 부서/직급/게시일
            department_postdate = section.find('div', attrs = {'class':'content_top_ty2'})
            postdate = department_postdate.find('span', attrs={'class':'txt2'})
            postdate = postdate.text.replace('\n','').replace('  ','')
            department = department_postdate.text.replace('\n','').replace('\xa0', '').replace('    ','').replace(postdate, '').replace('|', '')

            # 면접일
            date = section.dl.select('dd.txt1')
            date = date[0].text.strip() if len(date)>0 else '0000/00' # 없을 수도 있어서

            # 면접 결과
            result = section.select('.now_list')[0]
            result = result.find('dd').text.replace('\n','').replace('  ','').strip()

            # 제목
            head = section.select('.us_label')
            head = head[0].text.strip() if len(head)>0 else 'No Title'

            # 본문
            section_body = section.select('.content_body_ty1')[0]
            df_tit = section_body.select('.df_tit')
            df1 = section_body.select('.df1')
            body_dic = {"면접질문":'No Question', '면접답변 혹은 면접느낌':'No answer', '채용방식':'No 채용방식', '발표시기':'No 발표시기'}
            for title, context in zip(df_tit, df1):
                body_dic[title.text] = context.text.replace('\n\n','\n').replace('  ','').strip()

            # 배열에 한 게시글 담음
            reviews_per_page.append(Review(department, postdate, date, result,head, body_dic["면접질문"], body_dic["면접답변 혹은 면접느낌"], body_dic["채용방식"], body_dic["발표시기"]))

        # 한 페이지에서 가져온 게시글 배열을 리턴
        return reviews_per_page
    ```

- 이제 준비는 끝났습니다. request Session을 만들어 로그인 한 다음, 로그인한 세션 안에서 지정한 페이지들을 크롤링 해줍니다.
  - 페이지 수 만큼의 request 및 파싱을 동기식으로 처리하기 때문에 조금 느립니다.
  - 비동기적으로 request를 보내 빠르게 크롤링할까 했으나, 파이썬 비동기는 다뤄본 적이 없어 투자대비 효율이 떨어질 것 같아 당시엔 하지 않았습니다.
  
  ```python
  with requests.Session() as s:
      # 로그인
      req = s.get(LOGIN_PAGE)
      LOGIN_INFO = {
          'user[email]': ID,
          'user[password]': PW
      }
      login_req = s.post(LOGIN_PAGE, data=LOGIN_INFO)
      
      # 크롤링
      pages = [0]*(end_page-start_page+1)
      for i in range(start_page, end_page+1):
          pages[i-start_page]=get_one_page(BASE_URL+str(i))
  ```

- 파일을 저장해줍니다. 저장 형식은 각자 편한대로 수정하면 될 것 같습니다.

    ```python
    f = open(str(start_page)+'_'+str(end_page)+'.txt', mode='wt', encoding='utf-8')

    for idx, page in enumerate(pages):
        f.write("\n====================================================================\n")
        f.write(BASE_URL+str(idx+start_page))
        f.write("\n====================================================================\n")
        for post in page:
            f.write(post.department)
            f.write(" // 게시일 : "+post.postdate)
            f.write(" // 면접일 : "+post.date)
            f.write(" // 결과 : "+post.result)
            f.write("\n<< Title >>\n")
            f.write(post.head)
            f.write("\n<< 면접질문 >>\n")
            f.write(post.question)
            f.write("\n<< 면접답변 혹은 면접느낌 >>\n")
            f.write(post.answer)
            f.write("\n<< 채용방식 >>\n")
            f.write(post.way)
            f.write("\n<< 발표시기 >>\n")
            f.write(post.result_date)
            f.write("\n-------------------------------------------------------\n")

    f.close()    
    ```

