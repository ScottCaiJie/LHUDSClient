
<p align="center">
  <sub>© 2024-2027 Chengdu Born-soft Technology Co.,Ltd. All rights reserved.</sub>
</p>


<h1 align="center">LHUDSClient</h1>

<p align="center">
  <strong>面向汽车诊断工程师的 DoIP / UDS 桌面诊断工具</strong><br>
  Windows 绿色版 · 免安装 · 闭源免费
</p>
<p align="center">
  <img src="https://img.shields.io/badge/version-1.0-blue" alt="Version 1.0">
  <img src="https://img.shields.io/badge/platform-Windows%2010%20%2F%2011%20x64-lightgrey" alt="Windows x64">
  <img src="https://img.shields.io/badge/license-Freeware%20(Closed%20Source)-brightgreen" alt="Freeware">
</p>

## 产品简介

**LHUDSClient** 是一款面向汽车电子诊断工程师的桌面端工具软件。软件通过 **DoIP（Diagnostics over IP，ISO 13400-2）** 协议与车辆 ECU 建立 TCP 连接，执行 **UDS（Unified Diagnostic Services，ISO 14229）** 诊断服务，并对 ECU 响应进行自动校验，帮助工程师快速完成诊断验证、刷写测试和问题复现。

与一次性手动发送诊断请求的工具不同，LHUDSClient 以**任务列表（Task Plan）**为核心：你可以把多条诊断服务、等待步骤和文件传输编排成一份可保存、可复用的计划，然后一键顺序执行或循环压测，执行结果以颜色和状态直观呈现。任务计划保存为 JSON 文件，便于团队共享和回归测试。

软件为**闭源免费**软件，以二进制压缩包形式发布，解压即用，无需安装、无需注册、无需联网授权。

### 典型使用场景

| 场景 | 说明 |
|------|------|
| ECU 诊断功能验证 | 逐条验证会话控制、安全访问、DID 读写、例程控制等服务的响应 |
| 刷写流程调试 | 通过 FOTA 或 0x34/0x36/0x37 手动文件传输验证刷写链路 |
| 回归与压力测试 | 使用 CycleSend 循环执行任务计划，观察长时间稳定性 |
| 产线与台架测试 | 保存标准任务计划，现场一键执行，结果一目了然 |
| 问题复现与日志取证 | 实时日志 + 落盘日志，便于定位通信异常 |

---

## 核心特性

- **DoIP 连接管理**：TCP 连接、路由激活（Routing Activation）、Alive Check 自动应答，支持 Vehicle Announcement 监听。
- **完整 UDS 服务**：覆盖 17 种常用服务（0x10 ~ 0x85），涵盖会话控制、安全访问、DID 读写、DTC 管理、例程控制与文件传输。
- **FOTA 固件刷写**：内置整车平台刷写序列构建，支持传输进度与安装进度可视化。
- **文件传输**：0x34 / 0x35 / 0x36 / 0x37 / 0x38 完整流程，支持文件分块与 CRC 校验。
- **任务列表编排**：新建、编辑、删除、清空任务；支持 UDS 请求、Wait 等待等多种任务类型。
- **计划持久化**：SavePlan / LoadPlan 以 JSON 保存与加载任务计划，便于复用和团队共享。
- **执行控制**：SendList 单次顺序执行、CycleSend 循环执行并统计实际次数、Stop 随时中止。
- **响应校验**：支持多种 CheckRule 规则，自动判定 Pass / Fail，并以行背景色区分结果。
- **实时日志**：Tools → RealTime Log 独立窗口实时显示收发报文与执行过程，同时写入日志文件。
- **配置持久化**：所有网络参数、DoIP 地址、时序参数、日志设置均可在图形界面配置并保存。
- **多平台扩展**：安全算法、进度解析、FOTA 序列构建按接口分层，新增车型平台无需改动核心代码。

### 通信链路

```
用户操作 → 构建 UDS 请求 → DoIP 封装 → TCP 发送 → ECU 处理
        → TCP 接收 → DoIP 解帧 → UDS 响应解析 → 结果校验与展示
```

---

## 支持的 UDS 服务

| SID | 服务名称 | 说明 |
|-----|----------|------|
| 0x10 | DiagnosticSessionControl | 诊断会话控制 |
| 0x11 | ECUReset | ECU 复位 |
| 0x14 | ClearDiagnosticInformation | 清除诊断信息 |
| 0x19 | ReadDTCInformation | 读取 DTC 信息 |
| 0x22 | ReadDataByIdentifier | 按标识符读取数据 |
| 0x27 | SecurityAccess | 安全访问（Seed / Key） |
| 0x28 | CommunicationControl | 通信控制 |
| 0x2E | WriteDataByIdentifier | 按标识符写入数据 |
| 0x31 | RoutineControl | 例程控制 |
| 0x34 | RequestDownload | 请求下载 |
| 0x35 | RequestUpload | 请求上传 |
| 0x36 | TransferData | 数据传输 |
| 0x37 | RequestTransferExit | 传输退出 |
| 0x38 | RequestFileTransfer | 文件传输 |
| 0x3E | TesterPresent | 诊断仪在线（心跳） |
| 0x83 | AccessTimingParameter | 访问定时参数 |
| 0x85 | ControlDTCSetting | DTC 设置控制 |

## 支持的车型平台

| ID | 平台 | 安全算法 | 进度 DID | FOTA 特点 |
|----|------|----------|----------|-----------|
| 1 | default | default | 0xF1FF | CRC16 校验，多文件支持 |

新增平台只需实现安全算法接口、进度解析接口与 FOTA 序列构建接口，无需修改核心诊断逻辑。

## 系统要求

| 项目 | 要求 |
|------|------|
| 操作系统 | Windows 10 / 11，64-bit |
| 运行方式 | 绿色免安装，解压后直接运行 |
| 运行时依赖 | 已随包附带 Qt 运行库，无需额外安装 |
| 网络 | 本机网卡需与 ECU 处于同一网段 |
| 端口 | TCP 13400（DoIP）、UDP 13400（Vehicle Announcement） |
| 磁盘 | 建议解压到**可写目录**（如 `D:\Tools\LHUDSClient`），不要放在 `Program Files` 等只读目录 |

> 首次运行时 Windows 防火墙可能弹窗询问网络访问权限，请允许**专用网络**访问，否则无法与 ECU 建立 DoIP 连接。

---

## 快速开始

1. **下载**：获取 [LHUDSClient_1.0_Windows_x64.zip](Release/LHUDSClient_1.0_Windows_x64.zip)（GitHub Releases 页面同步提供最新版本）。
2. **解压**：将压缩包解压到任意**可写目录**，例如 `D:\Tools\LHUDSClient`。
3. **运行**：双击 `LHUDSClientV1.0.exe`。
4. **配置网络**：菜单 `Settings → Configure...`
   - `Network` 页：填写 ECU IP（默认 `172.30.1.11`）、ECU Port（默认 `13400`），本机 IP 留空表示自动绑定。
   - `DoIP` 页：确认 Source Address、Physical Address、Functional Address。
   - `Timing` 页：按需调整响应超时、发送间隔、TesterPresent 心跳及 ECU Reset 超时。
5. **确认连通**：确保本机与 ECU 同网段，`ping` 可通；点击 `OK` 保存配置。
6. **创建任务**：
   - 点击 `UDS Service` 新增一条诊断任务，选择服务、填写参数与校验规则。
   - 或点击 `FOTA` 选择固件文件，自动生成完整刷写任务序列。
7. **执行**：点击 `SendList` 顺序执行；勾选 `Cycle Mode` 并设置 `Target` 次数后点击 `CycleSend` 循环执行；`Stop` 可随时中止。
8. **查看日志**：菜单 `Tools → RealTime Log` 打开实时日志窗口；落盘日志见下方[配置与日志](#配置与日志)。

---

## 发布包结构

```
LHUDSClient_1.0_Windows/
├── README.md                                      # 产品介绍（本文件）
├── docs/
│   ├── LHUDSClient_SoftwareDesignSpecificationV1.0.docx   # 软件设计说明书
│   └── LHUDSClient_UserManualV1.0.docx                    # 用户操作手册
├── LICENSE.txt                                    # 软件使用许可协议
├── THIRD_PARTY_NOTICES.txt                        # 第三方组件及许可声明
└── Release/
    └── LHUDSClient_1.0_Windows_x64.zip            # Windows x64 运行包（解压即用）
```

`LHUDSClient_1.0_Windows_x64.zip` 解压后包含：

```
LHUDSClient_1.0_Windows_x64/
├── LHUDSClientV1.0.exe            # 主程序
├── LHUDSClientconfig.json         # 配置文件（首次保存时自动生成）
├── log/                           # 日志目录
├── platforms/ · imageformats/ · styles/ · translations/ · bearer/ · iconengines/
└── Qt5*.dll · D3Dcompiler_47.dll · libEGL.dll · libGLESV2.dll · opengl32sw.dll
```

---

## 配置与日志

### 配置文件

配置文件为 `LHUDSClientconfig.json`，位于**可执行文件同级目录**：

```
<解压目录>/LHUDSClientconfig.json
```

- 首次启动时若文件不存在，软件会自动以默认参数创建该文件。
- 推荐通过 `Settings → Configure...` 图形界面修改，点击 `OK` 即写回该文件。
- 如需恢复默认配置，关闭软件后删除该文件，重新启动即可自动重建。

主要参数说明：

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `platformId` | `1` | 车型平台：默认平台|
| `ecuIp` | `172.30.1.11` | ECU 的 IP 地址 |
| `ecuPort` | `13400` | ECU 的 DoIP 端口 |
| `localIp` | 空 | 本机绑定 IP，空表示自动 |
| `localPort` | `0` | 本机端口，0 表示由系统分配 |
| `sourceAddr` | `3712`（0x0E80） | DoIP 源地址（诊断仪 ID） |
| `physicalAddr` | `1858`（0x0742） | DoIP 物理目标地址 |
| `functionalAddr` | `3648`（0x0E40） | DoIP 功能目标地址 |
| `tpEnabled` | `false` | 是否周期发送 3E 80 心跳 |
| `tpInterval` | `2000` | TesterPresent 发送周期（ms） |
| `defaultTimeout` | `6000` | UDS 响应超时（ms） |
| `sendInterval` | `100` | 请求发送间隔（ms） |
| `ecuResetTimeout` | `30000` | ECU Reset 后等待 Vehicle Announcement 的超时（ms） |
| `udpPort` | `13400` | 监听 Vehicle Announcement 的 UDP 端口 |
| `logDir` | `<exe目录>/log/` | 日志保存目录 |
| `logEnabled` | `true` | 是否启用日志 |

> 注意：配置文件中的 DoIP 地址、超时等参数为 JSON 数值（十进制），图形界面中以十六进制显示。

### 日志

- 日志目录：默认 `<解压目录>/log/`
- 日志文件：`LHUdsClientRun.log`（追加写入）
- 日志格式：`[yyyy-MM-dd HH:mm:ss.zzz] [级别] 内容`，级别包含 `INFO`、`ERROR`、`SEND`、`RECV`
- 实时日志窗口：`Tools → RealTime Log`

---

## 文档

| 文档 | 说明 |
|------|------|
| [LHUDSClient_UserManualV1.0.docx](docs/LHUDSClient_UserManualV1.0.docx) | 用户操作手册：安装、界面说明、任务配置、FOTA 流程、FAQ |
| [LHUDSClient_SoftwareDesignSpecificationV1.0.docx](docs/LHUDSClient_SoftwareDesignSpecificationV1.0.docx) | 软件设计说明书：架构分层、协议实现、接口设计 |
| [LICENSE.txt](LICENSE.txt) | 软件使用许可协议 |
| [THIRD_PARTY_NOTICES.txt](THIRD_PARTY_NOTICES.txt) | 第三方组件及开源许可声明 |

---

## 常见问题

**Q1：连接不上 ECU？**

1. 确认本机 IP 与 ECU 在同一网段；
2. 确认 `ecuIp` / `ecuPort` 配置正确（默认端口 13400）；
3. 使用 `ping` 确认网络连通；
4. 确认 ECU 已上电且 DoIP 服务已启动；
5. 检查 Windows 防火墙是否拦截了本程序的专用网络访问。

**Q2：响应超时怎么办？**

在 `Settings → Timing` 中增大 `Default Timeout`。刷写类服务耗时较长，建议设置为 30000 ms 以上；同时可适当增大 `Send Interval`。

**Q3：配置文件在哪？修改后不生效？**

配置文件位于程序同级目录的 `LHUDSClientconfig.json`。修改后需重新创建会话才能应用新的网络参数；若手工编辑，请确保 JSON 格式正确。

**Q4：软件无法保存配置 / 提示无权限？**

程序需要在其所在目录写入配置文件与日志。请将软件解压到用户可写目录（如 `D:\Tools\`），不要放在 `C:\Program Files\`。

**Q5：日志文件在哪里？**

默认在 `<解压目录>/log/LHUdsClientRun.log`，可在 `Settings → Logging` 中修改日志目录。

**Q6：支持 Linux 吗？**

本期正式发布版本为 Windows 10/11 x64。软件架构本身跨平台，其他平台的发布计划请关注后续版本或与我们联系。

---

## 授权与声明

### 授权方式

- **闭源免费（Freeware / Closed Source）**：本软件以二进制形式免费提供下载与使用，**不提供源代码**。
- 具体授权范围、使用限制与免责条款以随附的 [LICENSE.txt](LICENSE.txt) 为准。

### 第三方组件

本软件使用了 Qt 等第三方组件，相关组件名称、版本与许可条款详见 [THIRD_PARTY_NOTICES.txt](THIRD_PARTY_NOTICES.txt)。使用本软件即表示您同时遵守相应第三方许可要求。

### 免责声明

本软件为诊断与测试工具，**刷写（FOTA）与写入类服务（0x2E / 0x31 / 0x34 等）存在损坏 ECU 的风险**。请在安全、可控的台架或测试环境中使用，并提前做好数据与固件备份。因使用本软件造成的任何直接或间接损失，由使用者自行承担。

---

## 关于我们

<p>
  <strong>Chengdu Born-soft Technology Co.,Ltd.</strong><br>
  专注于汽车电子诊断、车载网络与嵌入式软件工具研发。
</p>

- 产品反馈 / 问题咨询：**bornsoft@born-soft.com**
- 车型平台适配、定制开发与技术合作：欢迎通过上述邮箱联系我们
- 我们持续迭代 LHUDSClient，欢迎提交使用反馈与功能建议

> 本软件由我们免费提供，用于促进行业技术交流、提升诊断效率。欢迎在团队内部分享与推荐。

---

## 版本记录

### V1.0（首个公开发布版本）

- 支持 DoIP（ISO 13400-2）连接管理：TCP 连接、路由激活、Alive Check 应答
- 支持 17 种 UDS 服务（0x10 ~ 0x85）
- 支持任务列表编排、JSON 计划保存与加载、顺序执行与循环执行
- 支持 FOTA 固件刷写与 0x34/0x35/0x36/0x37/0x38 文件传输
- 支持实时日志窗口与落盘日志
- 支持图形化配置并持久化到 `LHUDSClientconfig.json`

---

<p align="center">
  <sub>© 2024-2027 Chengdu Born-soft Technology Co.,Ltd. All rights reserved.</sub>
</p>
