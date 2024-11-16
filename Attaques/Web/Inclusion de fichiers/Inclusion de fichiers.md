Inclure des fichiers dans du code qui ne devraient pas y être.

## PHP
| Function  | Read Content | Execute  | Remote URL |
| --- | --- | --- | --- |
| include()/include_once()  | x | x | x |
| require()/require_once()  | x | x |  |
| file_get_contents()  | x |  | x |
| fopen()/file()  | x |  |  |
## NodeJS
| Function  | Read Content | Execute  | Remote URL |
| --- | --- | --- | --- |
| fs.readFile()  | x |  |  |
| fs.sendFile()  | x |  |  |
| res.render()  | x | x |  |
## Java
| Function  | Read Content | Execute  | Remote URL |
| --- | --- | --- | --- |
| include | x |  |  |
| import | x | x | x |

## .Net
| Function  | Read Content | Execute  | Remote URL |
| --- | --- | --- | --- |
| @Html.Partial()  | x |  |  |
| @Html.RemotePartial()  | x |  | x |
| Response.WriteFile()  | x |  |  |
| include  | x | x | x |

