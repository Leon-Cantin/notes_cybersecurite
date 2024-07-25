Inclure des fichiers dans du code qui ne devraient pas y être.

| Function  | Read Content | Execute  | Remote URL |
| --- | --- | --- | --- |
| PHP |  |  |  |
| include()/include_once()  | x | x | x |
| require()/require_once()  | x | x |  |
| file_get_contents()  | x |  | x |
| fopen()/file()  | x |  |  |
| NodeJS  |  |  |  |
| fs.readFile()  | x |  |  |
| fs.sendFile()  | x |  |  |
| res.render()  | x | x |  |
| Java |  |  |  |
| include | x |  |  |
| import | x | x | x |
| .Net |  |  |  |
| @Html.Partial()  | x |  |  |
| @Html.RemotePartial()  | x |  | x |
| Response.WriteFile()  | x |  |  |
| include  | x | x | x |

