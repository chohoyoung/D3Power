D3
======================

# 5. 레이아웃
## 5.1 주 내용
1. 히스토그램과 파이차트 레이아웃
2. 간단한 트위닝
3. 트리, 서클팩, 스택 레이아웃
4. 생키 다이어그램과 워드 클라우드

이전에 설명했듯이 레이아웃은 직접 그림을 그리지 않는다. 그리고 컴포넌트처럼 호출하지 않는다. 또한 생성기와 달리 그림을 그리는 코드안에서 참조하지 않는다. 그럼 레이아웃은 뭐냐? 레이아웃은 데이터를 이미 선택한 형식으로 출력할 수 있게 포맷하는 처리 과정이라고 생각 하면 된다.
## 5.2 히스토그램
D3는 D3.histograme이라는 메서드가 있다. 이 메소드는 값을 자동으로 채우고 우리가 정의한 스케일에 따라서 막대그래프를 그리는데 필요한 설정을 만들어 준다. 우선 히스토그램 자체를 사용하는 방법을 아직 이해 못햇다 차후 정리함
## 5.3 파이차트
파이차트로 D3.pie() 레이아웃을 이용해서 파이를 그리기위한 데이터를 만들 수 있다.

    // 파이차트 레이아웃을 생성하고
	let pieChart = d3.pie();
	// 파이 차트에 데이터를 넘겨줘서 파이를 위한 데이터포맷형태의 데이터를 만든다.
	let yourPie = pieChart([1,1,2]);
	console.log(yourPie);

### 5.3.1 파이로 차트 그리기
데이터에 계산된 데이터를 넘겨주면된다. 그리고 속성 d에 arc를 넣어줘야 한다.

    d3.select('svg')
	.append('g')
	.attr('transform', 'translate(250, 250)')
	.selectAll('path')
	.data(yourPie)
	.enter()
	.append('path')
	.attr('d', newArc)
	.style('fill', 'blue')
	.style('opacity', .5)
	.style('stroke', 'black')
	.style('stroke-width', '2px')

그리고 파이쪽도 차후에 재정리 한다 v4로 올라가고 나서 참 ..많이 바뀐거 같다.
### 5.4 서클 팩 레이아웃
서클팩 레이아웃은 각 객체가 자신의 부모 객체안에 놓이는 구조로 계층구조를 쉽게 알 수 있다. 데이터 구조는 자식요소가 꼭 children 속성에 배열로 저장된 JSON객체를 받아야 한다. 사용법은 pack레이아웃을 생성하고 화면의 사이즈를 지정하고 각 항목의 사이즈를 나타낼 sum을 생성하고 sort로직을 추가하해도 되고 안해도된다. 그리고 d3.hierarchy를 사용해서 정렬하고 pack(데이터).descendants() 형태로 데이터에 넘겨주면된다 descendants는 데이터 각각을 node를 갖는 1차 배열로 만들어준다.

    let root = d3.hierarchy(data)
		.sum(function(d) { return d.size; })
      	.sort(function(a, b) { return b.value - a.value; });
		console.log(root)
		console.log(packChart(root).descendants());
		
		d3.select('svg')
		.selectAll('circle')
		.data(packChart(root).descendants())
		.enter()
		.append('circle')
		.attr('r', (d) => {	return d.r; })
		.attr('cx', (d) => { return d.x; })
		.attr('cy', (d) => { return d.y; })
		.style('fill', (d) => { return depthScale(d.depth); })
		.style('stroke', 'black')
		.style('stroke-width', '2px');

### 5.5 트리
