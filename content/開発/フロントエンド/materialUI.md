+++
title = "materialUI"
weight = 3
+++

## 参考
https://mui.com/material-ui/getting-started/installation/

## 手順
### 1.インストール
``` 
npm install @mui/material @emotion/react @emotion/styled
npm install @mui/x-data-grid
```

### 2.利用してみる
``` 
    <DataGrid
  rows={rows}
  columns={columns}
  initialState={{
    pagination: {
      paginationModel: { page: 0, pageSize: 5 },
    },
  }}
  pageSizeOptions={[5, 10]}
  checkboxSelection
/>
```