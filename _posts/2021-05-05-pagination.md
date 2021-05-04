---
layout: post
title: Paging on The Board List - Spring
---

#### 1. SQL Mapping XML (MySQL/MariaDB)

```
<select id="getListForPaging" resultType="com.er.domain.BoardVO">
		
		<![CDATA[
		select
			seq, subject, writer, hit, wdate
		from board2
		where seq > 0
		order by seq desc
		limit #{skip}, #{amount}
		]]>
		
	</select>
```

#### 2. Create Class of Paging Object

```
package com.er.domain;

import lombok.Getter;
import lombok.ToString;

@Getter
@ToString
public class Paging {

	// 현재 페이지
	private int pageNum;
	// 페이지당 게시글 수
	private int amount;
	// 페이지 넘기기
	private int skip;
	
	public Paging() {
		this(1,10);
		this.skip = 0;
	}

	public Paging(int pageNum, int amount) {
		// TODO Auto-generated constructor stub
		this.pageNum = pageNum;
		this.amount = amount;
		this.skip = (pageNum - 1) * amount;
	}

	public void setPageNum(int pageNum) {
		
		this.skip = (pageNum - 1) * this.amount;
		
		this.pageNum = pageNum;
	}

	public void setAmount(int amount) {
		
		this.skip = (this.pageNum - 1) * amount;
		
		this.amount = amount;
	}

	public void setSkip(int skip) {
		this.skip = skip;
	}
	
}

```

### 3. Mapper Interface

```
List<BoardVO> getListForPaging(Paging pg);
```

### 4. Service Interface

```
List<BoardVO> getList(Paging pg);
```

### 5. Implement Service

```
@Override
	public List<BoardVO> getList(Paging pg) {
		// TODO Auto-generated method stub
		log.info("get List for Paging: " + pg);
		
		return mapper.getListForPaging(pg);
	}
```

### 6. Change Controller

```
@GetMapping("/list")
	public void list(Paging pg, Model model) {
		log.info("list: " + pg);
		
		model.addAttribute("list", service.getList(pg));
	}
```
