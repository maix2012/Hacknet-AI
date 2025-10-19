

# Missions

## 重要提示（AI必须遵守）
> ⚠️ **AI处理规则**：
> - 所有Mission标签必须严格按文档顺序定义，顺序错误会导致游戏崩溃
> - 只能使用文档中明确定义的标签和属性，禁止推断或扩展
> - 所有ID、文件名、路径必须使用英文、数字、下划线，禁止中文
> - 文件路径必须使用正斜杠`/`，禁止反斜杠`\`

## 核心术语表
- **Mission（任务）**：游戏中的任务单元，通过邮件、任务数据库或文章触发
- **Goal（目标）**：任务完成条件，一个任务可以有多个goal
- **Flag（标志）**：布尔标记，用于跟踪游戏进度状态
- **Action（行动）**：通过函数或条件触发的游戏逻辑

## Mission基本结构
```xml
<?xml version="1.0" encoding="UTF-8"?>
<mission id="唯一任务ID" activeCheck="true" shouldIgnoreSenderVerification="false">
    <!-- 标签必须按以下顺序定义 -->
    <goals>...</goals>
    <missionStart>...</missionStart>
    <missionEnd>...</missionEnd>
    <nextMission>...</nextMission>
    <branchMissions>...</branchMissions>
    <posting>...</posting>
    <email>...</email>
</mission>
```

## Mission标签属性
### `<mission>` 根标签
- `id` (**必须**)：任务唯一标识符，英文数字组成
- `activeCheck` (可选)：是否自动检查目标完成，默认false
- `shouldIgnoreSenderVerification` (可选)：忽略发送者验证，默认false

## Goals（任务目标）
`<goals>` 标签**必须定义**，即使没有具体goal也要保留空标签。goal可以定义多个或不定义。

### 正确用法示例
```xml
<!-- ✅ 正确：goals标签必须存在 -->
<goals>
    <goal type="filedeletion" target="pc1" file="test.txt" path="home"/>
</goals>

<!-- ✅ 正确：空goals标签 -->
<goals></goals>

<!-- ❌ 错误：缺少goals标签 -->
<mission id="test">
    <email>...</email>  <!-- 顺序错误！ -->
</mission>
```

### Goal类型详解

#### filedeletion - 删除文件
```xml
<goal type="filedeletion" target="advExamplePC" file="asdf.txt" path="home"/>
```
- `target`：目标节点ID
- `file`：文件名
- `path`：文件路径

#### filechange - 修改文件内容
```xml
<goal type="filechange" target="pc1" file="test.txt" path="home" keyword="hello"/>
```
- `removal` (可选)：true表示关键词不能出现，默认false
- `caseSensitive` (可选)：是否区分大小写，默认false

**正确示例组合**：
```xml
<!-- ✅ 正确：将data替换为extension -->
<goal type="filechange" target="pc1" file="file.txt" path="home" keyword="extension"/>
<goal type="filechange" target="pc1" file="file.txt" path="home" keyword="data" removal="true"/>
```

#### getadmin - 获取管理员权限
```xml
<goal type="getadmin" target="advExamplePC"/>
```
- `target`：目标节点ID

#### getstring - 匹配附加内容
```xml
<goal type="getstring" target="password" />
```
- `target`：需要匹配的字符串

#### delay - 延迟时间
```xml
<goal type="delay" time="10.0"/>
```
- `time`：延迟时间，单位秒

#### hasflag - 检查flag
```xml
<goal type="hasflag" target="flagName"/>
```
- `target`：目标flag名称

#### fileupload - 上传文件
```xml
<goal type="fileupload" target="advExamplePC" file="asdf.txt" path="home" destTarget="introFactionHomeNode" destPath="Drop/Uploads"/>
```
- `target`：源节点ID
- `file`：文件名
- `path`：文件路径
- `destTarget`：目标节点ID
- `destPath`：目标路径
- `decrypt` (可选)：是否解密，默认false
- `decryptPass` (可选)：解密密码

#### AddDegree - 添加学历记录
```xml
<goal type="AddDegree" owner="John Stalvern" degree="Masters in Digital Security" uni="Manchester University" gpa="3.0"/>
```
- `owner`：目标人名
- `degree`：学位名称
- `uni`：学校名称
- `gpa`：绩点

#### wipedegrees - 删除学历记录
```xml
<goal type="wipedegrees" owner="John Stalvern"/>
```
- `owner`：目标人名

#### sendemail - 发送邮件
```xml
<goal type="sendemail" mailServer="jmail" recipient="mailuser123" subject="Email Subject!"/>
```
- `mailServer`：邮件服务器节点ID
- `recipient`：收件人
- `subject`：邮件主题

#### removeDeathRowRecord - 删除死亡记录
```xml
<goal type="removeDeathRowRecord" fname="Matt" lname="Trobbiani"/>
```
- `fname`：名
- `lname`：姓

#### getadminpasswordstring - 获取管理员密码字符串(DLC)
```xml
<goal type="getadminpasswordstring" target="advExamplePC"/>
```
- `target`：目标节点ID

## missionStart & missionEnd
### 正确用法
```xml
<!-- ✅ 正确：使用val属性传递数字参数 -->
<missionStart val="7">changeSong</missionStart>
<missionEnd val="1">addRank</missionEnd>

<!-- ✅ 正确：使用字符串参数 -->
<missionStart>setFaction:Entropy</missionStart>

<!-- ❌ 错误：参数格式错误 -->
<missionStart>addRank:1</missionStart>  <!-- 应该用val属性 -->
```

## nextMission & branchMissions
### nextMission
```xml
<nextMission IsSilent="false">Missions/NextMission.xml</nextMission>
<!-- 如果没有后续任务 -->
<nextMission>NONE</nextMission>
```

### branchMissions
```xml
<branchMissions>
    <branch>Missions/Branch1.xml</branch>
    <branch>Missions/Branch2.xml</branch>
</branchMissions>
```

## posting（任务数据库显示）
```xml
<posting title="任务标题" reqs="所需flag" requiredRank="所需积分">
任务描述内容
</posting>
```

## email（邮件内容）
```xml
<email>
    <sender>发送者</sender>
    <subject>邮件主题</subject>
    <body>邮件正文</body>
    <attachments>
        <note title="笔记标题">笔记内容</note>
        <link comp="目标节点ID"/>
        <account comp="目标节点ID" user="用户名" pass="密码"/>
    </attachments>
</email>
```

## 完整正确示例
```xml
<?xml version="1.0" encoding="UTF-8"?>
<mission id="exampleMission" activeCheck="true">
    <goals>
        <goal type="filedeletion" target="targetPC" file="data.txt" path="home"/>
        <goal type="getadmin" target="targetPC"/>
    </goals>
    
    <missionStart val="5">changeSong</missionStart>
    <missionEnd>setFaction:TestFaction</missionEnd>
    
    <nextMission IsSilent="false">Missions/Next.xml</nextMission>
    
    <branchMissions>
        <branch>Missions/Branch1.xml</branch>
    </branchMissions>
    
    <posting title="示例任务" reqs="startFlag" requiredRank="3">
这是一个示例任务描述
    </posting>
    
    <email>
        <sender>Admin</sender>
        <subject>重要任务</subject>
        <body>请完成这个任务</body>
        <attachments>
            <note title="说明">任务说明笔记</note>
            <link comp="targetPC"/>
        </attachments>
    </email>
</mission>
```

## Mission文件验证清单
在创建Mission文件时，请检查以下事项：
- [ ] `id`属性已定义且唯一
- [ ] 所有标签按正确顺序排列：goals → missionStart → missionEnd → nextMission → branchMissions → posting → email
- [ ] goals标签存在（即使为空）
- [ ] 所有文件路径使用正斜杠`/`
- [ ] 没有使用中文或特殊字符在ID、文件名中
- [ ] nextMission路径正确或为NONE
- [ ] 函数参数格式正确（数字用val属性，字符串用标签内容）

## 常见错误避免
1. **标签顺序错误**：email标签不能出现在goals之前
2. **路径格式错误**：使用`Missions/Folder/file.xml`而不是`Missions\Folder\file.xml`
3. **中文使用错误**：所有ID、文件名必须英文
4. **参数格式错误**：数字参数用val属性，字符串参数用标签内容

