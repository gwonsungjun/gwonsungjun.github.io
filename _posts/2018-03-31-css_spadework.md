---
title: '오늘의 CSS 삽질'  
layout: post  
tags :  
- css
category: css
---

**html, table 태그 끊겨서 보이는 문제 해결**{: style="display:inherit;text-align:center;"}

---

### 오늘의 CSS 삽질!

``` html
 <html>
    <body>
        <div>
            <table>
                ~
            </table>
            <table>
                ~
            </table>
        </div>
    </body>
</html>
```

크롬에서 html table을 출력하고 스크롤을 내리다보니 중간에 끊겨서 보인다. 드래그하면 또 보이긴한다. 아래 그림을 보면 중간에 끊겨있는 것이 보인다.

![css](/assets/images/usingimages/css.png)

그래서 크롬개발자도구로 디버깅해본 결과 body사이즈가 div 사이즈에 비해 작아서 출력을 못했다.(추측)   
수동으로 body에 높이를 고정px style="height:10000px" 주니깐 잘 보인다.   
확실한 원인을 이렇다고 말하기에는 css 지식이 아직 부족하다. 혹시 원인을 아시는 분 가르쳐주시면 감사하겠습니다.   
그래도 해결방법을 찾았다. javascript로 해결!   
너무 css로만 해결할려고 했지 javascirpt를 쓰면 이렇게 쉽게 해결할 수 있다는것을 왜 뒤늦게 깨달았을까!   

혹시나 같은 현상이 또 발생했을때 삽질을 줄이기 위해 기록을 남긴다!   

```js
var divHeight = document.getElementById('pop').getBoundingClientRect().height;
document.body.style.height = divHeight+'px';
```

해당 아이디의 div 태그의 height을 구하고 body에 구해진 height을 넣어서 body 사이즈를 변경한다.
