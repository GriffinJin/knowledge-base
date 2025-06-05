# Enum Usage Finder

根据枚举id列表，寻找到目标项目项目中使用了这些枚举的菜单。

## 使用教程

1. 使用 ExportTranRef.java 工具导出`菜单包含的窗体清单`

   工具位置：

    代码仓库：git@10.10.20.8:toolpackage/tranchange.git

   分支：TranAndFormScan

   替换 workspacePath 为实际项目目录，直接运行该工具的 `main` 方法，生成结果文件存放在项目目录跟目录下，名为 `menuRefTask-{timestamp}.xlsx`

2. 使用工具 `enum_usage_finder` 导出 `使用枚举的交易清单`

   *该工具暂时没有通过代码仓库管理，保存在本地。*

   替换 `resources` 目录下的资源文件。

   `enum_ids.xlsx` 为枚举ID清单，通过 `胡君男.VerenaHu` 获得

   `menu_form_mapping.xlsx` 为 `菜单包含的窗体清单`

   ![image-20250605155602571](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250605155602571.png)

   通过模块的方式运行该 python 脚本，命令如下：

   `python -m enum_usage_finder.main`

3. 结果为 `resources` 目录下的 `output.xlsx`