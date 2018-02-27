---
title: 文件上传和Excel解析
date: 2017-06-15 10:51:23
tags: [Java,文件上传]
categories: work
---
  
文件上传的技术有很多，最近在做一键上传Excel省市级功能发现OCUpload很好用，借以此篇总结。

<!-- more -->

## 一、一键上传 
### 1.1、原理分析：
一键上传在页面中会有一个隐藏的form表单和iframe，和一个页面可见按钮，当点击该按钮是触发表单提交，表单提交本来是异步的，需要跳转页面，
但将表单提交到隐藏iframe中后，相当于本页面没有刷新
```
<button type="button" id="btn">一键上传</button>
<form action="xxx.action" type="post" enctype="multipart/form-data" target="myframe" id="testForm" style="display: none">
	<input type="file" name="file"/>
	<input type="button" value="提交" id="submitBtn" onclick="submitFrom()"/>
</form>
<%--表单提交到iframe中--%>
<iframe name="myframe" frameborder="0" style="display: none;"></iframe>
<script>
	function submitFrom(){
		// 异步提交本来要跳转连接，但是因为提交到了隐藏的iframe中，页面看着感觉没有发生跳转
		$("#testForm").submit();
	}

	$(function () {
		// 点击页面可见的按钮 触发表单提交
		$("#btn").click(function () {
			$("#submitBtn").trigger('click');
		})
	})
</script>
```

### 1.2、Jquery OCUpload
ocupload插件已经将一切封装好了，我们只需要引入jquery.ocupload-x.x.x.js，对页面中的一个按钮绑定事件，配置下列属性，即可实现一键上传操作：
```
$(function () {  
	$("#uploadfile").upload({  
		action: '要提交的controller路径',  
		name: 'file',  //文件上传input属性的默认值
		enctype:"multipart/form-data",
		params: {  
			 
		},  			// 需要提交到controller的其他参数
		// 选择文件后触发的事件
		// 如果想实现控制文件的类型: 需要关闭自动提交，判断文件类型、再手动提交  
		onSelect: function (self, element) {   
			this.autoSubmit = false;
			var filename = this.filename();
			//声明正则 .*名字任意  \. 转义 
			var reg = /^.*\.(xls|xlsx)$/;
			if(reg.test(filename)){
				this.submit();
			}else{
				alert('请上传以xls或xlsx结尾文件！');
			}
		}, 
		// 触发文件提交事件	
		onSubmit: function (self, element) {  
			 
		},  
		// 文件上传完成后
		onComplete: function (data, self, element) {  
			 
		}  
	});  
});  
```  
  
### 1.3、Struts提供的文件上传功能

## 二、POI解析Excle表格
Apache POI是Apache组织的开源的函式库，提供API给Java程序对 Office格式档案读和写的功能。
由于Excel在07年之前的后缀名为.xls，07年之后后缀名为.xlsx，所以解析两者的对象也不同，分别为HSSF和XSSF
### 2.1、入门
1、导入POI maven坐标
```
<dependency>
	<groupId>org.apache.poi</groupId>
	<artifactId>poi</artifactId>
	<version>3.9</version>
</dependency>

<dependency>
	<groupId>org.apache.poi</groupId>
	<artifactId>poi-ooxml</artifactId>
	<version>3.9</version>
</dependency>

<dependency>
	<groupId>org.apache.poi</groupId>
	<artifactId>poi-ooxml-schemas</artifactId>
	<version>3.9</version>
</dependency>
```
2、POI EXCEL文档结构类：
- HSSFWorkbook  文档工作簿
- HSSFSheet 	文档的每个Sheet 
- HSSFCell 		每个单元格
- HSSFHeader sheet 
- HSSFFooter sheet
...

3、解析思路：
先获取WorkBook工作簿对象 - 获取Sheet对象，并遍历每个Sheet - 获取每个Sheet下的行数(排除行首) - 获取每个单元格

4、工具类：
网上相关工具类有很多，下面这个两种格式的都可以上传
```
package com.leahshi.utils;

import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;

import org.apache.log4j.Logger;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.springframework.web.multipart.MultipartFile;

public class POIUtils {
 
	private static Logger logger = Logger.getLogger(POIUtils.class);
	private final static String xls = "xls";
	private final static String xlsx = "xlsx";

	/**
	 * 读入excel文件，解析后返回
	 * 
	 * @param file
	 * @throws IOException
	 */
	public static List<String[]> readExcel(MultipartFile file) throws IOException {
		// 检查文件
		checkFile(file);
		// 获得Workbook工作薄对象
		Workbook workbook = getWorkBook(file);
		// 创建返回对象，把每行中的值作为一个数组，所有行作为一个集合返回
		List<String[]> list = new ArrayList<String[]>();
		if (workbook != null) {
			for (int sheetNum = 0; sheetNum < workbook.getNumberOfSheets(); sheetNum++) {
				// 获得当前sheet工作表
				Sheet sheet = workbook.getSheetAt(sheetNum);
				if (sheet == null) {
					continue;
				}
				// 获得当前sheet的开始行
				int firstRowNum = sheet.getFirstRowNum();
				// 获得当前sheet的结束行
				int lastRowNum = sheet.getLastRowNum();
				// 循环除了第一行的所有行
				for (int rowNum = firstRowNum + 1; rowNum <= lastRowNum; rowNum++) {
					// 获得当前行
					Row row = sheet.getRow(rowNum);
					if (row == null) {
						continue;
					}
					// 获得当前行的开始列
					int firstCellNum = row.getFirstCellNum();
					// 获得当前行的列数
					int lastCellNum = row.getPhysicalNumberOfCells();
					String[] cells = new String[row.getPhysicalNumberOfCells()];
					// 循环当前行
					for (int cellNum = firstCellNum; cellNum < lastCellNum; cellNum++) {
						Cell cell = row.getCell(cellNum);
						cells[cellNum] = getCellValue(cell);
					}
					list.add(cells);
				}
			}
			// workbook.close();
		}
		return list;
	}

	public static void checkFile(MultipartFile file) throws IOException {
		// 判断文件是否存在
		if (null == file) {
			logger.error("文件不存在！");
			throw new FileNotFoundException("文件不存在！");
		}
		// 获得文件名
		String fileName = file.getOriginalFilename();
		// 判断文件是否是excel文件
		if (!fileName.endsWith(xls) && !fileName.endsWith(xlsx)) {
			logger.error(fileName + "不是excel文件");
			throw new IOException(fileName + "不是excel文件");
		}
	}

	public static Workbook getWorkBook(MultipartFile file) {
		// 获得文件名
		String fileName = file.getOriginalFilename();
		// 创建Workbook工作薄对象，表示整个excel
		Workbook workbook = null;
		try {
			// 获取excel文件的io流
			InputStream is = file.getInputStream();
			// 根据文件后缀名不同(xls和xlsx)获得不同的Workbook实现类对象
			if (fileName.endsWith(xls)) {
				// 2003
				workbook = new HSSFWorkbook(is);
			} else if (fileName.endsWith(xlsx)) {
				// 2007 及2007以上
				workbook = new XSSFWorkbook(is);
			}
		} catch (IOException e) {
			logger.info(e.getMessage());
		}
		return workbook;
	}

	public static String getCellValue(Cell cell) {
		String cellValue = "";
		if (cell == null) {
			return cellValue;
		}
		// 把数字当成String来读，避免出现1读成1.0的情况
		if (cell.getCellType() == Cell.CELL_TYPE_NUMERIC) {
			cell.setCellType(Cell.CELL_TYPE_STRING);
		}
		// 判断数据的类型
		switch (cell.getCellType()) {
		case Cell.CELL_TYPE_NUMERIC: // 数字
			cellValue = String.valueOf(cell.getNumericCellValue());
			break;
		case Cell.CELL_TYPE_STRING: // 字符串
			cellValue = String.valueOf(cell.getStringCellValue());
			break;
		case Cell.CELL_TYPE_BOOLEAN: // Boolean
			cellValue = String.valueOf(cell.getBooleanCellValue());
			break;
		case Cell.CELL_TYPE_FORMULA: // 公式
			cellValue = String.valueOf(cell.getCellFormula());
			break;
		case Cell.CELL_TYPE_BLANK: // 空值
			cellValue = "";
			break;
		case Cell.CELL_TYPE_ERROR: // 故障
			cellValue = "非法字符";
			break;
		default:
			cellValue = "未知类型";
			break;
		}
		return cellValue;
	}
}

```


## 三、使用PinYin4J生成城市简码
因为导入省市级数据后，需要生成城市简码，比如省市区首字母，城市拼音等，需要使用到PinYin4J这个工具。
```
package com.leahshi.utils;

import java.util.Arrays;

import net.sourceforge.pinyin4j.PinyinHelper;
import net.sourceforge.pinyin4j.format.HanyuPinyinCaseType;
import net.sourceforge.pinyin4j.format.HanyuPinyinOutputFormat;
import net.sourceforge.pinyin4j.format.HanyuPinyinToneType;
import net.sourceforge.pinyin4j.format.exception.BadHanyuPinyinOutputFormatCombination;

public class PinYin4jUtils {
	/**
	 * 将字符串转换成拼音数组
	 * 
	 * @param src
	 * @return
	 */
	public static String[] stringToPinyin(String src) {
		return stringToPinyin(src, false, null);
	}

	/**
	 * 将字符串转换成拼音数组
	 * 
	 * @param src
	 * @return
	 */
	public static String[] stringToPinyin(String src, String separator) {

		return stringToPinyin(src, true, separator);
	}

	/**
	 * 将字符串转换成拼音数组
	 * 
	 * @param src
	 * @param isPolyphone
	 *            是否查出多音字的所有拼音
	 * @param separator
	 *            多音字拼音之间的分隔符
	 * @return
	 */
	public static String[] stringToPinyin(String src, boolean isPolyphone,
			String separator) {
		// 判断字符串是否为空
		if ("".equals(src) || null == src) {
			return null;
		}
		char[] srcChar = src.toCharArray();
		int srcCount = srcChar.length;
		String[] srcStr = new String[srcCount];

		for (int i = 0; i < srcCount; i++) {
			srcStr[i] = charToPinyin(srcChar[i], isPolyphone, separator);
		}
		return srcStr;
	}

	/**
	 * 将单个字符转换成拼音
	 * 
	 * @param src
	 * @return
	 */
	public static String charToPinyin(char src, boolean isPolyphone,
			String separator) {
		// 创建汉语拼音处理类
		HanyuPinyinOutputFormat defaultFormat = new HanyuPinyinOutputFormat();
		// 输出设置，大小写，音标方式
		defaultFormat.setCaseType(HanyuPinyinCaseType.LOWERCASE);
		defaultFormat.setToneType(HanyuPinyinToneType.WITHOUT_TONE);

		StringBuffer tempPinying = new StringBuffer();

		// 如果是中文
		if (src > 128) {
			try {
				// 转换得出结果
				String[] strs = PinyinHelper.toHanyuPinyinStringArray(src,
						defaultFormat);

				// 是否查出多音字，默认是查出多音字的第一个字符
				if (isPolyphone && null != separator) {
					for (int i = 0; i < strs.length; i++) {
						tempPinying.append(strs[i]);
						if (strs.length != (i + 1)) {
							// 多音字之间用特殊符号间隔起来
							tempPinying.append(separator);
						}
					}
				} else {
					tempPinying.append(strs[0]);
				}

			} catch (BadHanyuPinyinOutputFormatCombination e) {
				e.printStackTrace();
			}
		} else {
			tempPinying.append(src);
		}

		return tempPinying.toString();

	}

	public static String hanziToPinyin(String hanzi) {
		return hanziToPinyin(hanzi, " ");
	}

	/**
	 * 将汉字转换成拼音
	 * 
	 * @param hanzi
	 * @param separator
	 * @return
	 */
	public static String hanziToPinyin(String hanzi, String separator) {

		// 创建汉语拼音处理类
		HanyuPinyinOutputFormat defaultFormat = new HanyuPinyinOutputFormat();
		// 输出设置，大小写，音标方式
		defaultFormat.setCaseType(HanyuPinyinCaseType.LOWERCASE);
		defaultFormat.setToneType(HanyuPinyinToneType.WITHOUT_TONE);

		String pinyingStr = "";
		try {
			pinyingStr = PinyinHelper.toHanyuPinyinString(hanzi, defaultFormat,
					separator);
		} catch (BadHanyuPinyinOutputFormatCombination e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return pinyingStr;
	}

	/**
	 * 将字符串数组转换成字符串
	 * 
	 * @param str
	 * @param separator
	 *            各个字符串之间的分隔符
	 * @return
	 */
	public static String stringArrayToString(String[] str, String separator) {
		StringBuffer sb = new StringBuffer();
		for (int i = 0; i < str.length; i++) {
			sb.append(str[i]);
			if (str.length != (i + 1)) {
				sb.append(separator);
			}
		}
		return sb.toString();
	}

	/**
	 * 简单的将各个字符数组之间连接起来
	 * 
	 * @param str
	 * @return
	 */
	public static String stringArrayToString(String[] str) {
		return stringArrayToString(str, "");
	}

	/**
	 * 将字符数组转换成字符串
	 * 
	 * @param str
	 * @param separator
	 *            各个字符串之间的分隔符
	 * @return
	 */
	public static String charArrayToString(char[] ch, String separator) {
		StringBuffer sb = new StringBuffer();
		for (int i = 0; i < ch.length; i++) {
			sb.append(ch[i]);
			if (ch.length != (i + 1)) {
				sb.append(separator);
			}
		}
		return sb.toString();
	}

	/**
	 * 将字符数组转换成字符串
	 * 
	 * @param str
	 * @return
	 */
	public static String charArrayToString(char[] ch) {
		return charArrayToString(ch, " ");
	}

	/**
	 * 取汉字的首字母
	 * 
	 * @param src
	 * @param isCapital
	 *            是否是大写
	 * @return
	 */
	public static char[] getHeadByChar(char src, boolean isCapital) {
		// 如果不是汉字直接返回
		if (src <= 128) {
			return new char[] { src };
		}
		// 获取所有的拼音
		String[] pinyingStr = PinyinHelper.toHanyuPinyinStringArray(src);

		// 创建返回对象
		int polyphoneSize = pinyingStr.length;
		char[] headChars = new char[polyphoneSize];
		int i = 0;
		// 截取首字符
		for (String s : pinyingStr) {
			char headChar = s.charAt(0);
			// 首字母是否大写，默认是小写
			if (isCapital) {
				headChars[i] = Character.toUpperCase(headChar);
			} else {
				headChars[i] = headChar;
			}
			i++;
		}

		return headChars;
	}

	/**
	 * 取汉字的首字母(默认是大写)
	 * 
	 * @param src
	 * @return
	 */
	public static char[] getHeadByChar(char src) {
		return getHeadByChar(src, true);
	}

	/**
	 * 查找字符串首字母
	 * 
	 * @param src
	 * @return
	 */
	public static String[] getHeadByString(String src) {
		return getHeadByString(src, true);
	}

	/**
	 * 查找字符串首字母
	 * 
	 * @param src
	 * @param isCapital
	 *            是否大写
	 * @return
	 */
	public static String[] getHeadByString(String src, boolean isCapital) {
		return getHeadByString(src, isCapital, null);
	}

	/**
	 * 查找字符串首字母
	 * 
	 * @param src
	 * @param isCapital
	 *            是否大写
	 * @param separator
	 *            分隔符
	 * @return
	 */
	public static String[] getHeadByString(String src, boolean isCapital,
			String separator) {
		char[] chars = src.toCharArray();
		String[] headString = new String[chars.length];
		int i = 0;
		for (char ch : chars) {

			char[] chs = getHeadByChar(ch, isCapital);
			StringBuffer sb = new StringBuffer();
			if (null != separator) {
				int j = 1;

				for (char ch1 : chs) {
					sb.append(ch1);
					if (j != chs.length) {
						sb.append(separator);
					}
					j++;
				}
			} else {
				sb.append(chs[0]);
			}
			headString[i] = sb.toString();
			i++;
		}
		return headString;
	}
	
	public static void main(String[] args) {
		// pin4j 简码 和 城市编码 
		String s1 = "中华人民共和国"; 
		String[] headArray = getHeadByString(s1); // 获得每个汉字拼音首字母
		System.out.println(Arrays.toString(headArray)); // [Z, H, R, M, G, H, G]
		
		String s2 ="长城" ; 
		System.out.println(Arrays.toString(stringToPinyin(s2,true,","))); // [zhang,chang, cheng]
		
		String s3 ="长";
		System.out.println(Arrays.toString(stringToPinyin(s3,true,","))); // [zhang,chang]
	}
}
 
```
关于解决多音字问题，在找了很多资料，看到http://blog.csdn.net/lkx94/article/details/53860253 这篇博文写的不错~   
  
  
  
  
  
  
  
  
  
  














