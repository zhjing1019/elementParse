# elementParse
Element(2.9.0版本)，el-table模块源码解析，添加自己的理解和注释！ Element(version 2.9.0), el-table module source analysis, add your own understanding and comments!
## Element中el-table中对于树形表头的算法，以及计算树形表头的colspan和rowspan的算法，对其中的理解和部分注释代码事例
```
//计算单元格的colSpan
/**
 * 计算单元格的colsapn，用于合并列
 * 计算单元格所属的层级
 * @param {*} originColumns，是指第一层级的元素
 */
const convertToRows = originColumns => {
  let maxLevel = 1;
  const traverse = (column, parent) => {
    if (parent) {
      //计算当前元素属于第几个层级
      column.level = parent.level + 1;
      if (maxLevel < column.level) {
        //计算最大层级
        maxLevel = column.level;
      }
    }
    if (column.children) {
      let colSpan = 0;
      column.children.forEach(subColumn => {
        //进行递归
        traverse(subColumn, column);
        colSpan += subColumn.colSpan;
      });
      column.colSpan = colSpan;
    } else {
      column.colSpan = 1;
    }
  };

  //设置第一层级的leave = 1； 和第一层级的colspan
  originColumns.forEach(column => {
    column.level = 1;
    traverse(column);
  });
  //计算有几个tr；得到rows的数组[];
  const rows = [];
  for (let i = 0; i < maxLevel; i++) {
    rows.push([]);
  }
  //获得所有的th项， 展开树形数据
  const allColumns = getAllColumns(originColumns);
  //计算单元格的rowspan;
  //得到循环的数组
  allColumns.forEach(column => {
    if (!column.children) {
      //如果没有children
      column.rowSpan = maxLevel - column.level + 1;
    } else {
      //如果有children
      column.rowSpan = 1;
    }
    rows[column.level - 1].push(column);
  });

  return rows;
};
```
