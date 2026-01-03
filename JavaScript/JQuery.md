# jQuery

## 개념

DOM 조작, 이벤트 처리, Ajax를 **간편하게** 사용할 수 있는 JavaScript 라이브러리

### 특징
- "Write less, do more" 철학
- 크로스 브라우저 호환성
- 2006년 John Resig 개발
- 현재는 사용 감소 추세 (Vanilla JS, React 등으로 대체)

## 기본 문법
```javascript
$(선택자).메서드();

// $ = jQuery
jQuery(선택자).메서드();
```

## CDN 추가
```html
<!-- jQuery 3.x -->
<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>

<!-- 사용 -->
<script>
  $(document).ready(function() {
    // jQuery 코드
  });
  
  // 축약형
  $(function() {
    // jQuery 코드
  });
</script>
```

## 선택자
```javascript
// ID
$('#myId')

// Class
$('.myClass')

// 태그
$('div')

// 복합
$('div.myClass')
$('#myId .myClass')

// 속성
$('input[type="text"]')

// 가상 선택자
$('li:first')
$('li:last')
$('li:even')
$('li:odd')
```

## DOM 조작

### 내용 변경
```javascript
// 텍스트
$('#myDiv').text('안녕');
const text = $('#myDiv').text();

// HTML
$('#myDiv').html('<b>안녕</b>');
const html = $('#myDiv').html();

// 값 (input)
$('#myInput').val('값 설정');
const value = $('#myInput').val();
```

### 요소 추가/제거
```javascript
// 추가
$('#list').append('<li>마지막에</li>');
$('#list').prepend('<li>첫번째에</li>');
$('<li>새 항목</li>').appendTo('#list');

// 제거
$('#myDiv').remove();
$('#myDiv').empty();  // 자식만 제거
```

### 속성 조작
```javascript
// 속성
$('img').attr('src', 'new.jpg');
const src = $('img').attr('src');

// 여러 속성
$('img').attr({
  src: 'new.jpg',
  alt: '이미지'
});

// 속성 제거
$('img').removeAttr('src');

// 프로퍼티 (checked, disabled 등)
$('#checkbox').prop('checked', true);
```

### CSS 조작
```javascript
// 스타일
$('#myDiv').css('color', 'red');
$('#myDiv').css({
  color: 'red',
  fontSize: '20px'
});

// 클래스
$('#myDiv').addClass('active');
$('#myDiv').removeClass('active');
$('#myDiv').toggleClass('active');
$('#myDiv').hasClass('active');  // true/false
```

## 이벤트 처리
```javascript
// 클릭
$('#btn').click(function() {
  alert('클릭됨');
});

// on 메서드 (권장)
$('#btn').on('click', function() {
  alert('클릭됨');
});

// 여러 이벤트
$('#btn').on('click mouseenter', function() {
  console.log('이벤트 발생');
});

// 이벤트 위임
$('#list').on('click', 'li', function() {
  console.log($(this).text());
});

// 이벤트 제거
$('#btn').off('click');
```

### 자주 사용하는 이벤트
```javascript
$('#input').on('input', function() {
  console.log($(this).val());
});

$('#input').on('focus', function() {
  $(this).css('border', '2px solid blue');
});

$(window).on('scroll', function() {
  console.log('스크롤:', $(window).scrollTop());
});
```

## 효과 및 애니메이션
```javascript
// 표시/숨김
$('#myDiv').show();
$('#myDiv').hide();
$('#myDiv').toggle();

// 페이드
$('#myDiv').fadeIn();
$('#myDiv').fadeOut();
$('#myDiv').fadeToggle();

// 슬라이드
$('#myDiv').slideDown();
$('#myDiv').slideUp();
$('#myDiv').slideToggle();

// 애니메이션
$('#myDiv').animate({
  left: '250px',
  opacity: 0.5
}, 1000);
```

## Ajax
```javascript
// GET 요청
$.get('/api/data', function(data) {
  console.log(data);
});

// POST 요청
$.post('/api/data', { name: 'Kim' }, function(response) {
  console.log(response);
});

// AJAX
$.ajax({
  url: '/api/data',
  type: 'GET',
  dataType: 'json',
  success: function(data) {
    console.log('성공:', data);
  },
  error: function(xhr, status, error) {
    console.log('에러:', error);
  }
});
```

## 체이닝
```javascript
$('#myDiv')
  .css('color', 'red')
  .slideUp(500)
  .slideDown(500)
  .addClass('active');
```

## jQuery vs Vanilla JS

| 작업 | jQuery | Vanilla JS |
|------|--------|-----------|
| 선택 | `$('#id')` | `document.querySelector('#id')` |
| 클릭 | `$('#btn').click(fn)` | `btn.addEventListener('click', fn)` |
| 텍스트 | `$('#div').text('Hi')` | `div.textContent = 'Hi'` |
| HTML | `$('#div').html('<b>Hi</b>')` | `div.innerHTML = '<b>Hi</b>'` |
| CSS | `$('#div').css('color', 'red')` | `div.style.color = 'red'` |

### 비교 예시
```javascript
// jQuery
$('#btn').click(function() {
  $('#myDiv').text('클릭됨').css('color', 'red');
});

// Vanilla JS
document.querySelector('#btn').addEventListener('click', () => {
  const div = document.querySelector('#myDiv');
  div.textContent = '클릭됨';
  div.style.color = 'red';
});
```

## 장단점

### 장점
- 간결한 코드
- 크로스 브라우저 호환성
- 풍부한 플러그인

### 단점
- 추가 라이브러리 필요 (용량)
- 최신 브라우저에서는 불필요
- 성능 저하 가능

## 현대 웹에서의 사용

**jQuery 사용 감소 이유**:
- 최신 브라우저의 표준화
- Vanilla JS 기능 향상
- React, Vue 등 프레임워크 보급

**여전히 사용하는 경우**:
- 레거시 프로젝트
- 빠른 프로토타입
- 간단한 웹사이트

## 참고 자료

- [jQuery 공식 사이트](https://jquery.com/)
- [jQuery API 문서](https://api.jquery.com/)
- [You Might Not Need jQuery](https://youmightnotneedjquery.com/)