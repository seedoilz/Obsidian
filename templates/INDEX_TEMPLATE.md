<%*
const dv = app.plugins.plugins["dataview"].api;
const filename = "index";
const query1 = `LIST FROM #project`;
const query2 = `TABLE file.mtime AS "Last Modified", tags AS "tags" 
WHERE file.name != "index" AND file.name != "INDEX_TEMPLATE"
SORT file.mtime DESC LIMIT 10`;
let today = tp.date.now("YYYY-MM-DD")
// 获取输入到inputDate
let inputDate = await tp.system.prompt("输入示例："+today,today)
// 格式化变量titleName成年-月-日_周几
titleName = window.moment(inputDate, "YYYY-MM-DD", true).format("YYYY-MM-DD_ddd")
// 获取当前文件创建时间
let createTime = new Date(tp.file.creation_date())
// 获取当前文件修改时间
let modificationDate = new Date(tp.file.last_modified_date()); // 假设tp.file.last_modified_date()返回一个可被Date对象解析的日期字符串
let options = {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
  hour: 'numeric',
  minute: 'numeric',
  second: 'numeric',
  hour12: true
};
let createdTime = createTime.toLocaleString('zh-CN', options);
let modifiedDate = modificationDate.toLocaleString('zh-CN', options);
const linter = `---
aliases: 
title: index
date created: ${modifiedDate}
date modified: ${createdTime}
---`;
const tFile = tp.file.find_tfile(filename);
const queryOutput1 = await dv.queryMarkdown(query1);
const queryOutput2 = await dv.queryMarkdown(query2);

const queryOutput = '## Projects\n' + queryOutput1.value + '## Recently Edited\n' + queryOutput2.value

// write query output to file
await app.vault.modify(tFile, queryOutput);
%>