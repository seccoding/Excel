# Excel
Java 에서 엑셀파일을 읽고 쓰는 유틸리티<br/>
xls 와 xlsx를 모두 지원함.

## Release Note
### 2.0.0 (2019.02.20)
> 1. WriteOption.setContents(List<String[]> contents); 삭제.
> 2. WriteOption<T>.setContents(List<T> contents); 추가
> 2-1. String[] 대신 Data Class 로 사용함.

## 사용 방법
### maven 사용
1. Repository 추가<pre>
   &lt;repositories&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&lt;repository&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;id&gt;bintray&lt;/id&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;url&gt;http://jcenter.bintray.com</url&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;snapshots&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;enabled&gt;false&lt;/enabled&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;/snapshots&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&lt;/repository&gt;
&lt;/repositories&gt;
   </pre>
   
1. dependency 추가<pre>
   &lt;dependency&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&lt;groupId&gt;io.github.seccoding&lt;/groupId&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&lt;artifactId&gt;Excel&lt;/artifactId&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&lt;version&gt;2.0.0&lt;/version&gt;
	&lt;/dependency&gt;
   </pre>
   
### maven dependency에 Excel-2.0.0.jar 파일을 추가할 경우
1. Excel-2.0.0.jar파일을 C:\에 복사합니다.
1. Maven 명령어를 이용해 .m2 Repository 에 Excel-2.0.0.jar 를 설치(저장)합니다.<pre>mvn install:install-file -Dfile=C:\Excel-2.0.0.jar -DgroupId=io.github.seccoding -DartifactId=Excel -Dversion=2.0.0 -Dpackaging=jar</pre>
1. 본인의 Project/pom.xml 에 dependency를 추가합니다.<pre>
	&lt;dependency&gt;
	&nbsp;&nbsp;&nbsp;&nbsp;&lt;groupId&gt;io.github.seccoding&lt;/groupId&gt;
	&nbsp;&nbsp;&nbsp;&nbsp;&lt;artifactId&gt;Excel&lt;/artifactId&gt;
	&nbsp;&nbsp;&nbsp;&nbsp;&lt;version&gt;2.0.0&lt;/version&gt;
	&lt;/dependency&gt;
</pre>

### 소스코드를 사용할 경우
1. Clone or Download 를 클릭합니다.
1. Download ZIP 을 클릭해 소스코드를 다운로드 받습니다.
1. Excel/pom.xml의 dependencies를 본인의 Project/pom.xml 에 붙혀넣습니다.
1. Excel/src 이하의 자바코드를 본인의 Project에 붙혀넣습니다. 
---
## Excel File 읽기
<pre>
package io.github.seccoding.excel;

import java.util.List;
import java.util.Map;

import io.github.seccoding.excel.annotations.Field;
import io.github.seccoding.excel.annotations.Require;
import io.github.seccoding.excel.option.ReadOption;
import io.github.seccoding.excel.read.ExcelRead;

public class ExcelReadTest {

	static ReadOption ro = new ReadOption();

	public static void main(String[] args) {

		ro.setFilePath("excel_file_path_with_file_name");
		ro.setOutputColumns("B");
		ro.setStartRow(7);
		ro.setSheetName("Sheet1");
		
		test1();
		test2();
		test3();
	}

	public static void test1() {
		Map&lt;String, String> result = new ExcelRead&lt;>().read(ro);

		System.out.println(result);
	}

	public static void test2() {
		TestClass result = new ExcelRead&lt;TestClass>().readToObject(ro, TestClass.class);

		System.out.println(result.getNo());
		System.out.println(result.getColumnName());
		System.out.println(result.getType());
	}
	
	public static void test3() {
		System.out.println("test3");
		String result = new ExcelRead().getValue(ro, "B3");
		System.out.println(result);
	}

	public static class TestClass {

		@Field("B")
		@Require // 값이 항상 존재하는 컬럼을 지정. 탐색 ROW를 지정할 때 사용.
		private List&lt;String> no;
		
		@Field("C")
		private List&lt;String> columnName;
		
		@Field("F")
		private List&lt;String> type;

		public List&lt;String> getNo() {
			return no;
		}

		public void setNo(List&lt;String> no) {
			this.no = no;
		}

		public List&lt;String> getColumnName() {
			return columnName;
		}

		public void setColumnName(List&lt;String> columnName) {
			this.columnName = columnName;
		}

		public List&lt;String> getType() {
			return type;
		}

		public void setType(List&lt;String> type) {
			this.type = type;
		}

	}

}

</pre>

---
## Excel File 쓰기
<pre>

import java.io.File;
import java.util.ArrayList;
import java.util.List;

import io.github.seccoding.excel.annotations.Field;
import io.github.seccoding.excel.option.WriteOption;
import io.github.seccoding.excel.write.ExcelWrite;

/**
 * ExcelWriteTest Example
 * 
 * @author Min Chang Jang (mcjang1116@gmail.com)
 */
public class ExcelWriteTest {

	public static void main(String[] args) {

		WriteOption&lt;TestVO> wo = new WriteOption&lt;TestVO>();
		wo.setSheetName("Test");
		wo.setFileName("test.xlsx");
		wo.setFilePath("C:\\Users\\mcjan\\Desktop");

		List&lt;String> titles = new ArrayList&lt;String>();
		titles.add("Title1");
		titles.add("Title2");
		titles.add("Title3");
		titles.add("Title4");
		wo.setTitles(titles);

		List&lt;TestVO> contents = new ArrayList&lt;TestVO>();
		contents.add(new TestVO(1, "ABC", true, "=1+1"));
		contents.add(new TestVO(2, "DEF", true, "=2+2"));
		contents.add(new TestVO(3, "HIJ", true, "=3+3"));
		wo.setContents(contents);

		File excelFile = ExcelWrite.write(wo);
	}

	public static class TestVO {
		
		@Field("Title1")
		private int id;
		
		@Field("Title2")
		private String content;
		
		@Field("Title3")
		private boolean isTrue;
		
		@Field("Title4")
		private String formula;

		public TestVO(int id, String content, boolean isTrue, String formula) {
			this.id = id;
			this.content = content;
			this.isTrue = isTrue;
			this.formula = formula;
		}

		public int getId() {
			return id;
		}

		public void setId(int id) {
			this.id = id;
		}

		public String getContent() {
			return content;
		}

		public void setContent(String content) {
			this.content = content;
		}

		public boolean isTrue() {
			return isTrue;
		}

		public void setTrue(boolean isTrue) {
			this.isTrue = isTrue;
		}

		public String getFormula() {
			return formula;
		}

		public void setFormula(String formula) {
			this.formula = formula;
		}

	}

}

</pre>
