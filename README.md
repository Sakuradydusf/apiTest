# apiTest
接口测试简明攻略

## 代码规范
1. 命名规范建议
	1. 测试类命名规范
		* 推荐：API名称+Test.如:GetLatestQuestionsByIdTest
		* 不推荐：getLatestQuestionsByIdTest,类名应符合驼峰命名规则
	2. BVT用例命名规范
		* 推荐:test+API名称+BVTCase.如:testGetLatestQuestionsById
		* 不推荐:Test+API名称+BVTCase
	3. 异常用例命名规范
		* 推荐:test+API名称+That+异常场景_should+预期结果.如:testGetVolumnByGradeThatGradeNotExists_ShouldRetrunEmpy
		* 不推荐:testGetVolumnByGradeThatGrade1
2. 注释
	1. 测试类注释 说明测试的接口
	```java
	/**
 	 * Test Class for {@link com.iflytek.cycore.question.api.BookIdxService.Iface#renderBookByPse(String, String, String)}.
 	 *
 	 * @author XX.
 	 */
	```
	2. 测试用例注释 说明用例的场景和预期结果
	```java
	@Test
    @Case(value = "查询五四学制小学语文所有书本",expected = "接口返回五四学制小学语文所有书本")
	```
3. 断言
	1. 断言要写成方法，尽量避免使用多个独立断言，减少代码冗余
	```java
	private void assertBooks(final List<TBookIdx> actualBooks,
			String phaseName, String subjectCode, String subjectName,
			List<String> grades){
			//断言代码
			}
	@Test
	@Case("BVT Case2, 根据学科学段获取书本目录树")
	private void testRenderBookByVersionBVTCase2() throws TException{
		List<TBookIdx> books = bookIdxLogClient.renderBookByVersion("05", "01");
		assertBooks(books, "高中", "01", "语文",
				Lists.newArrayList("10", "11", "12", "19"));
	}
   ```
## Testng小套路
1. 接口对某一类异常数据返回结果一致，可以使用DataProvoider
	* 使用DataProvoider
	```java
	@DataProvider
    private Object[][] nullTestData() {
        return new Object[][] {
                {null,null,null},
                {null,"05","02"},
                {"03",null,"02"},
                {"03","02",null},
        };
    }

    @Test(dataProvider = "nullTestData")
    public void testRenderBookByPseThatParameterIsNull_ShouldReturnEmptyBookIdx(String phase, String subject, String eduSys) throws TException{
        List<TBookIdx> books = bookIdxLogClient.renderBookByPse(phase,subject,eduSys);
        assertThat(books).isEmpty();
    }
	```
	* 不使用DataProvoider
	```java
	@Test
	public void testRenderBookByPseThatPhaseParameterIsNull_ShouldReturnEmptyBookIdx(String phase, String subject, String eduSys) throws TException{
        List<TBookIdx> books = bookIdxLogClient.renderBookByPse(null,subject,eduSys);
        assertThat(books).isEmpty();
    }
	
	@Test
	public void testRenderBookByPseThatSubjectParameterIsNull_ShouldReturnEmptyBookIdx(String phase, String subject, String eduSys) throws TException{
        List<TBookIdx> books = bookIdxLogClient.renderBookByPse(phase,null,eduSys);
        assertThat(books).isEmpty();
    }
	
	@Test
	public void testRenderBookByPseThatSubjectParameterIsNull_ShouldReturnEmptyBookIdx(String phase, String subject, String eduSys) throws TException{
        List<TBookIdx> books = bookIdxLogClient.renderBookByPse(phase,subject,null);
        assertThat(books).isEmpty();
    }
	```


