D3
======================

# 4. 차트
## 4.1 주 내용
1. 축 컴포넌트의 생성과 포맷
2. 차트의 선과 영역생성기
3. SVG 요소의 다중형으로 구성된 복잡한 도형 생성

## 4.2 차트의 기본 원리
D3에서 차트 생성은 크게 3가지로 분류 할수 있다. 
1. 생성기
2. 컴포넌트
3. 레이아웃

### 4.2.1 생성기
생성기는 데이터를 입력받고 이 데이터를 통해서 화면의 객체 생성을 위한 SVG그림 코드를 반환한다. d3.line(), d3.area(), d3.arc(), d3.diagonal() 등의 메소드가 있다.
### 4.2.2 컴포넌트
컴포넌트는 특정 차트 컴포넌트를 그리는데 필요한 일련의 화면 객체를 생성한다. 가장 많이 쓰는 컴포넌트는 d3.svg.axis로써 포토샵같은데 보면 가장위와 좌측에 있는 그리드 축을 그리는 역할을 한다. 또한 d3.svg.brush는 브러시 셀렉터에 필요한 화면 요소를 모두 생성한다. 생성기가 단위단위 하나하나씩을 생성한다면 컴포넌트는 특정 기능을 위한 많은 요소를 생성한다.
### 4.2.3 레이아웃 (정리 필요)
레이아웃은 데이터를 입력받아서 특정 위치와 크기로 그리는데 필요한 데이터 속성을 동적 또는 정적으로 추가하는 기능이다. 추가 정리해서 더 정리 하도록 하겠음.
## 4.3 축 생성
### 4.3.1 데이터 표시
컴포넌트의 d3.svg.axis()를 사용해서 축을 그릴 수 있다. 아래 코드처럼 사용해서 그릴 수 있다.

    // axis는 axisTop, axisLeft, axisBottom, axisRight가 있으며 left, top이런 것은 label의 방향을 뜻한다.
	let xAxis = d3.axisBottom(xScale);
	d3.select('svg').append('g').attr('id', 'xAxisG').call(xAxis);
	let yAxis = d3.axisRight(yScale);
	d3.select('svg').append('g').attr('id', 'yAxisG').call(yAxis);

Axis는 라인을 그리는 <path.domain>과 각 요소요소를 그리는 <g.tick>를 생성한다. 그리고 <g.tick>안에는 <line>과 <text>가 있다. line은 삐죽 나온 부분을 text는 삐죽 나온 상태의 값을 표시해준다. 그리고 기본적으로 path.domain은 아무런 색이 없고 오직 stroke만 있다. 우리가 fill스타일을 통해서 칠할수 있지만 그러면 색이 칠해져서 보기가 좀 그렇다.그리고 axis는 여러 함수를 제공해준다. tickSize나 ticks같은 함수를 제공해준다. 그것을 사용해서 사이즈나 tick갯수를 강제 조정할 수 있다.

    // axis는 axisTop, axisLeft, axisBottom, axisRight가 있으며 left, top이런 것은 label의 방향을 뜻한다.
	// Axis는 tickSize나 ticks메소드를 제공하는데 각각 axis의 값을 매핑하고 axis를 리턴한다.
	let xBottomAxis = d3.axisBottom(xScale).tickSize(500).ticks(4);
	let yRightAxis = d3.axisRight(yScale).tickSize(500).ticks(16);

## 4.4 복잡한 화면 객체
인구 조사나 통계 데이터등 데이터 분포 측면에서 원이나, 사각형으로 데이터를 표현하기 어려운 케이스도 있다. 가령 주가 변동같은것은 이런 전통적인 산포도 보다 박스 플롯(boxplot)형태가 유용하다. 박스플롯의 값은 아래와 같다.
1. 최대값
2. 제 3사분위수 등 해당 분포에서 높은 값
3. 중앙값이나 평균값
4. 제 1사분위수 등 해당 분포에서 낮은 값
5. 최소값

이 데이터를 사용해서 박스 플롯을 그릴 수 있다.
## 4.5 선 그래프와 보간법
선 그래프는 점과 점을 연결해서 만든다. 점을 연결하는 선과 선에 의해 구분된 안쪽과 바깥쪽 영역은 데이터에 대한 정보를 보여준다. 선 그래프는 정적 데이터 시각화이지면, 변화를 표현하기도 한다.
### 4.5.1 점을 이용해 선그리기
d3.line()을 사용해서 라인을 그릴수 있다. line은 생성기며 생성기는 그림코드를 반환한다. 그래서 아래처럼 사용 할 수 있다.

    // d3.line은 데이터에 따라 path에서 사용할 path를 생성해서 넘긴다.
	let tweetLine = d3.line()
	.x((d) => { return xScale(d.day); })
	.y((d) => { return yScale(d.tweets); })

	console.log(tweetLine(data))

	d3.select('svg')
	.append('path')
	.attr('d', tweetLine(data))
	.attr('fill', 'none')
	.attr('stroke', 'darkred')
	.attr('stroke-width', 2);

### 4.5.2 다중 생성기로 여러선 그리기
### 4.5.3 선 보간법
기본적으로 직선으로 선이 그려진다. 만약 둥글게나 굴곡있게 하고 싶으면 할 수 있다. 각라인의 curve()함수안에 우리가 사용할 보간방법을 선택하면 된다. 보관방법은 **[여기](https://github.com/d3/d3-shape#curves)**에서 살펴볼수 있습니다.

    // 각라인별 interpolate(보간법)을 변경함.
	tweetLine.curve(d3.curveCardinal);
	retweetLine.curve(d3.curveBasis);
	favoritesLine.curve(d3.curveStep);

### 4.5.4 Line, Area
Line과 Area는 위에서 말한대로 path를 생성해주는 생성자다. Line같은 경우 뒤에 Z를 붙여줘야 하지만 Area같은경우 그럴필요가 없다. Line은 Z만 붙여줘도 이것은 최초값에서 마지막값에 선을 연결한다. 그리고 area는 재미있는 기능하나가 있는데 그것은 y1과 y0이다. x와 y1은 line과동일하지만 y0은 y1의 상대적인 높이다.

## 4.6 정리
1. 셀렉션과 바인딩 된 데이터와 생성기와 컴포넌트의 사용
2. x축, y축등 차트 요소를 생성하는 d3컴포넌트, 축 컴포넌트 Axis
3. d3생성기 line, area
4. each()메서드를 사용
5. 박스 플롯
6. 스트림 그래프

