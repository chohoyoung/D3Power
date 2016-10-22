D3
======================

# 3. 데이터 주도 설계와 상호작용
## 3.1 주 내용
1. 그래픽 요소에 상호작용성 추가
2. 색상을 효과적으로 사용
3. 전통적인 HTML을 로딩해 팝업으로 사용
4. 외부 SVG아이콘 로딩해서 차트에 넣기

## 3.2 프로젝트 아키텍처
### 3.2.1 데이터
CVS나 JSON같은 데이터
### 3.2.2 리소스
SVG, HTML, DOM요소
### 3.2.3 그림
PNG, SVG같은 그림
### 3.2.4 CSS
스타일시트
### 3.2.5 외부라이브러리
colorbrewer.js는 색상 팔래트를 제공한다.
## 3.3 대화형 스타일과 DOM
### 3.3.1 이벤트
D3는 각각의 요소에 속성을 찾아내거나 버튼을 동적으로 생성하거나, 이벤트도 동적으로 만들어줄 수 있다. 이 지원되는 기능을 이용하면 데이터에 따라서 이벤트를 동적으로 생성할 수 있다.
### 3.3.2 그래픽 전환 에니메이션
딱딱한 움직임보다 duration, transition을 통해서 부드럽게 만드는게 좋다. delay()는 에니메이션 시작시간을 늦추는데 먼가 약간의 지연을 통한 시각적요소가 등장하거나, 아니면 이벤트의 체인시에 사용될수 있다.(몇초뒤에 이 이벤트를 호출한다.)
### 3.3.3 DOM 조작
인라인 함수에서 this를 이용하거나 node() 메서드를 이용해서 가져올 수 있다.

    // 인라인 함수에서 this란?
	d3.select('circle').each(function(d, i) {
		console.log(d);	// 바인딩 된 데이터
		console.log(i);	// 인덱스
		console.log(this);	//dom객체에 접근한다. 바인딩 된 데이터가 아니다

	});
	
	// 가장 첫번째 circle을 찾아 dom을 반환한다.
	d3.select('circle').node();

마우스 이벤트 자체를 제어 할 수 있는데 예를 들어 원안에 텍스트가 있고 원에 mouseover할경우 Text가 커지며 원에 마우스가 out되엇을 경우 text가 작아지는 기능이 잇을경우 Text가 원보다 커질경우 마우스가 원밖에 있고 커진 Text안에 있을경우 mouseout이벤트가 먹지 않는다 그럴경우 text에 마우스 이벤트를 없에야 한다. 아래처럼

    teamG.select('text').style('pointer-event','none');

### 3.3.4 색상
#### 색상혼합
D3는 색상을 혼합하게 해주는 interpolate()를 제공한다. 여기엔 D3에서 제공해주는 d3.interpolateHsl이나 interpolateLab이 있다. interpolateHsl은 색상(hue), 농도(chroma), 휘도(fuminance)에 기초해 중간색을 넘겨준다. interpolateLab은 L(light)와 A, B는 색의 대응공간 (color opponent space)를 뜻하는데 이 정보를 통해서 색상 정보를 넘겨준다.

    // 이런 값을 HCL색상 모델을 통해서 사용할 수 있다. HCL은 색상, 농도, 휘도에 기초한 데이터를 넘겨준다.
	let ybRampHCL = d3.scaleLinear().interpolate(d3.interpolateHsl).domain([0, maxValue]).range(['yellow', 'blue']);
	
	// LAB은 L 과 A,B를 기초데이터로 중간 색을 반환한다. L은 밝기 이고 A, B는 색의 대응공간(color opponent space)를 뜻한다.
	let ybRampLAB = d3.scaleLinear().interpolate(d3.interpolateLab).domain([0, maxValue]).range(['yellow', 'blue']);

#### 잘 구분되는 색상
D3에서는 잘 구분되는 색상을 모아서 제공해준다. d3.schemeCategory10부터 해서 20,20b,20c등 여러 색상 팔래트를 제공하는데 점점더 추가될것이고 자세한 정보는 **[여기](https://github.com/d3/d3-scale)**에서 살펴볼수 있다.

    // scaleOrdinal은 칼라값의 배열을 받고 그리고 range를 이미 가진 scale을 넘겨준다.
	// schemeCategory10는 d3에서 제공하는 잘구분되는 색상 10가지를 배열로 넘겨준다. 참고로 20도 있고 20c이런것도 있다.
	let colorCategory10 = d3.scaleOrdinal(d3.schemeCategory10).domain(['UEFA','CONMEBOL','CAF','AFC']);

#### 외부에서 제공하는 팔래트
D3에서 제공하는 색상 팔래트도 있는데 그밖에 신시아 브루어(Cynthia Brewer)에 기초한 색상 체계를 사용하는 방법도 있다. d3js.org에서는 colorbrewer.js와 colorbrewer.css를 제공하는데 각 색상의 팔래트 배열은 브루어의 색상 체계와 대응한다.

    //quantize는 range값의 갯수로 분류를 잡아서 사용할 수 있게 한다.
	let colorQuantize3 = d3.scaleQuantize().domain([0, maxValue]).range(colorbrewer.Reds[3]);
	let colorQuantize5 = d3.scaleQuantize().domain([0, maxValue]).range(colorbrewer.Reds[5]);

## 3.4 리소스 사용
### 3.4.1 그림
이미지는 xlink:href를 사용해서 넣을 수 있다.

    // insert를 사용해서 image를 text앞에 삽입한다.
	d3.selectAll('g.overallG').insert('image', 'text')
	.attr('xlink:href', function(d) {
		return '../common/images/' + d.team + ".png";
	})
	.attr("width", '45px')
	.attr('height', '20px')
	.attr('x', '-22')
	.attr('y', '-10');

### 3.4.2 HTML
우리는 미리 만들어지고 디자인 되어진 HTML을 가져다 쓸 수도 있을 것이다. 방식은 간단한데 기존 csv나 json처럼 html을 로드해서 html에 담으면 되는데 아래와 같이 할 수 있다. html이나 xml이나 d3에서 제공해주는 d3.text()로 사용해서 데이터를 받아올수 있다.

    // text로 html을 데이터를 가져오고ㄴ 데이터를 html로 넣는다.
	d3.text('modal.html', function(d) {
		// body에 div를 추가후 div에 modal을 주고 그안에 modal.html의 코드를 넣는다.
		d3.select("body").append("div").attr("id", "modal").html(d);
	});
### 3.4.3 외부 SVG파일 사용 (차후 정리)



