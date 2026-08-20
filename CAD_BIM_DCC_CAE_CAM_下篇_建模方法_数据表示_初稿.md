# 同样叫“建模”，为什么结果完全不同？

## CAD / BIM / DCC / CAE / CAM 的数据表示与建模方法对比

如果只看界面，CAD、BIM、DCC、CAE、CAM 都可能出现“点、线、面、实体、网格”和三维视图。但界面相似不代表模型相同。

在 CAD 中，模型要表达精确几何和设计意图；在 CAE 中，模型要转成可以计算的离散问题；在 CAM 中，模型要变成加工过程；在 BIM 中，模型要成为带属性和关系的构件数据库；在 DCC 中，模型要适合拓扑、贴图、变形、动画和渲染。

因此，建模不是一个统一动作，而是五类软件对“对象如何被表示、修改、验证和交付”的不同回答。

## 一、先看五种模型到底在表示什么

| 软件类别 | 建模对象 | 典型数据表示 | 第一评价标准 |
|---|---|---|---|
| CAD | 精确几何与设计意图 | B-Rep、NURBS、特征、约束、参数 | 精度、可编辑、可制造 |
| CAE | 可求解的物理问题 | 网格、材料、边界条件、载荷 | 网格质量、收敛、结果可信度 |
| CAM | 制造过程 | 工序、刀具、刀路、机床坐标、NC/G-code | 可加工、防碰撞、节拍和成本 |
| BIM | 构件、属性与关系 | 族、类型、实例、对象关系、IFC | 语义完整、可协调、可统计 |
| DCC | 视觉和动画资产 | Polygon、SubD、雕刻、程序化、UV、绑定 | 拓扑、变形、渲染、管线兼容 |

这个表揭示了一个容易被忽略的事实：CAE 和 CAM 的“建模”甚至不一定是在创造新的设计几何。它们更多是在重新组织 CAD 或 BIM 提供的数据，使其适合求解或制造。

## 二、CAD：建的是几何，也是设计意图

### 1. B-Rep 不等于简单的 NURBS

Shapr3D 对 CAD 技术基础的解释给出了一个重要区分：B-Rep，即边界表示，用边、面、曲线、曲面和拓扑关系描述一个实体的边界；NURBS 是其中可能使用的一类曲线或曲面表达方式，但 B-Rep 不等于 NURBS。

CAD 的几何内核负责布尔、拉伸、扫掠、放样、倒角、圆角和拓扑处理。Parasolid、ACIS、CGM、Granite、ShapeManager 等内核，决定了软件如何计算几何以及不同系统之间如何交换数据。

换句话说，用户看到的是“拉伸”或“圆角”按钮，内核处理的却是曲线、曲面、拓扑、容差和实体有效性。模型是否稳健，往往与几何内核和数据转换质量密切相关。

### 2. 参数化建模保存的是制造意图

典型参数化 CAD 的流程是：

草图 → 几何约束 → 尺寸 → 拉伸/旋转/扫掠 → 圆角/倒角/阵列 → 装配 → 工程图和 BOM。

特征树不仅记录最终形状，还记录形状是怎样被构造出来的。修改孔径、壁厚或阵列间距时，相关几何可以沿着依赖关系重新计算。

这就是参数化 CAD 与纯粹曲面或网格建模的关键区别：它保存的不只是结果，还保存了制造和修改的逻辑。

### 3. NURBS、参数化、隐式和 SubD 是四种不同的思维方式

DemystifyingPLM 将现代 CAD 建模归纳为四种范式：

- NURBS 曲面建模：以连续曲线和曲面为核心，适合工业设计、汽车外形和复杂曲面。
- 参数化特征 B-Rep：以草图、约束和特征历史为核心，适合机械设计、装配、文档和供应链交付。
- 隐式或 SDF 建模：以场函数表达形状，适合拓扑优化、晶格、复杂结构和增材制造。
- SubD：以可细分的控制网格表达形体，兼顾概念塑形和较灵活的拓扑编辑。

这四种范式不是简单的“新旧替代”。在一些产品团队中，它们可能形成分层工作流：用 NURBS 或 SubD 探索概念，用参数化 CAD 完成工程文档，再用隐式方法处理复杂结构或增材制造。

需要注意的是，素材中的“99%”等比例数字是文章作者的判断，不能未经核验就写成行业统计。可以保留其工作流观点，但要去掉未经独立来源支持的绝对数字。

## 三、CAE：建模的重点从几何转向可计算性

### 1. 为什么 CAD 模型不能直接拿来算

CAD 模型是为设计和制造建立的，可能包含大量对制造有意义、对仿真却没有帮助的细节：

- 小圆角和倒角；
- 螺纹、刻字和装饰面；
- 远离关注区域的孔和内部腔体；
- 装配间隙和不影响当前问题的细小结构。

这些细节会迫使网格在局部变得非常密集，增加单元数量、降低网格质量，甚至直接让网格生成失败。

Spatial 的 CAE 前处理文章把问题说得很直接：求解器往往不是有限元流程中最容易出问题的部分，真正拖慢项目的是几何导入、拓扑修复、去特征和网格生成。文章估计工程师约有 38% 的分析时间花在前处理上；这个数字应当作为该文的估计，而不是普遍定律。

### 2. CAE 建模是一条前处理管线

一个典型 CAE 前处理流程可以拆成：

CAD 几何导入 → 诊断和修复 → 去特征 → 分区 → 网格生成 → 网格质量检查 → 材料定义 → 边界条件和载荷 → 求解 → 后处理。

CADfix CAE 将其概括为诊断、修复、去特征、分区和网格化五个阶段。它还强调，自动去除孔、圆角、倒角和内部结构时，应该保留接触区、应力集中区等功能区域。

这说明 CAE 的“模型好坏”不是看它是否忠实保留了所有 CAD 细节，而是看它能否在合理的计算成本下得到稳定、可解释的结果。

### 3. 网格不是低配版的 CAD

网格是对几何和物理问题的离散化。四面体、六面体、棱柱层和混合网格各有适用场景，网格尺寸也应该围绕关注区域、梯度变化和边界层来决定。

Cadence 从 CFD 角度比较 NURBS 与网格时指出，连续曲面和离散网格各有适用范围：平滑、可由高阶曲线准确表达的边界可以使用连续表示；复杂或适合离散坐标系的问题，则可能更适合网格。

因此，CAE 的模型判断标准包括网格质量、边界条件是否合理、结果是否收敛、敏感性是否可解释，而不是模型看起来是否光滑。

## 四、CAM：建的是刀路和工艺，不是另一个几何模型

### 1. CAD 结束的地方，CAM 开始

DemystifyingPLM 对 CAD 和 CAM 的一句概括很适合作为分界线：

CAD 创建和验证几何，输出模型；CAM 把几何转成刀路和 G-code，输出机器指令。

CAM 的模型通常包含：

- 零件几何；
- 毛坯和余量；
- 夹具和装夹状态；
- 刀具库；
- 工序顺序；
- 切削参数；
- 机床坐标系；
- 刀路和后处理规则。

所以，同一个孔在 CAD 中是直径、深度和公差，在 CAM 中还要变成钻头或铣刀、进给速度、转速、进退刀和避让策略。

### 2. CAM 程序员和软件各自负责什么

UTEC 对 CNC 工作流的拆解很有代表性：

CAD 阶段建立或导入带尺寸和公差的几何；CAM 阶段定义加工操作，软件计算刀具中心的精确路径；后处理阶段再把通用刀路转换成某台机床能执行的代码。

CAM 程序员通常决定：

- 先粗加工还是先处理关键基准；
- 使用哪种刀具和加工策略；
- 加工深度、步距、进给和转速；
- 工件坐标系和装夹方式；
- 进退刀、避让和防碰撞策略。

软件则负责大量几何计算和路径生成。这个分工解释了为什么 CAM 不能被理解为“点一下自动生成路径”：制造经验和机床约束仍然不可替代。

### 3. CAM 的合格标准是“能不能安全、高效地做出来”

一个视觉上漂亮的 CAD 设计，不一定适合当前刀具、机床、材料和夹具。CAM 需要检查：

- 是否过切；
- 是否碰撞；
- 是否存在无法到达的区域；
- 后处理代码是否适配目标机床；
- 加工时间和材料去除率是否合理；
- 最终表面质量是否满足要求。

因此，CAM 建模的核心不是继续增加几何细节，而是把设计几何翻译成可靠的制造过程。

## 五、BIM：建的是对象、关系和信息

### 1. CAD 的墙和 BIM 的墙不是同一种东西

CAD 里的墙可以是几条线、一个填充或一组面。它表达形状和位置，但本身未必知道这是墙。

BIM 里的墙通常是一个有类型、材料、厚度、性能、造价、工程量和宿主关系的对象。门可以依附于墙，房间可以由边界围合，楼层、空间、设备和系统之间存在结构关系。

Revit 建模实践文章把 Revit 描述为带参数和信息的建筑数据库，而不是单纯的三维绘图工具。学术论文则从族的造型、约束、参数设置、运算符和规则等角度，比较了 Revit、MSCE、Paramodel 和 ueBIM。

### 2. BIM 参数化的核心是族、类型和实例

BIM 的参数化逻辑与机械 CAD 的特征树不同：

- 族描述一类构件的行为和参数；
- 类型表示某种规格或配置；
- 实例是项目中的具体对象；
- 宿主、连接、空间和专业关系让对象进入建筑系统；
- 属性和明细表把模型连接到工程量、成本、进度和运维。

因此，BIM 的模型质量除了几何准确，还取决于语义完整性和信息可用性。一个外形正确但属性缺失的模型，可能仍然无法支持施工和运维。

## 六、DCC：建的是可看、可动、可交接的资产

### 1. Polygon 和 NURBS 的评价标准不同

Polygon 模型由顶点、边和面组成，适合实时显示、角色、环境、游戏资产和渲染。NURBS 以数学曲线和曲面表达形状，更适合要求连续曲率和制造精度的工业设计。

Alignify 的总结很适合作为方向判断：

- 以实体制造为目标，通常需要 CAD、NURBS、STEP 或 IGES；
- 以屏幕表现和实时引擎为目标，通常需要 Polygon、FBX、glTF 或 USD。

这不是说 DCC 没有精确工具，也不是说 CAD 不能做外观，而是两个系统的第一评价标准不同。

### 2. DCC 建模还没有结束

在 DCC 中，网格形体只是资产的一部分。一个能进入生产的资产还要考虑：

- 拓扑是否适合细分和变形；
- UV 是否可用；
- 材质和纹理是否能交给下游；
- 绑定和动画是否稳定；
- 面数和 LOD 是否满足实时或渲染要求；
- 版本和格式在部门之间是否可靠。

CG Lounge 对 Maya、Houdini、Blender 的定位强调了生产管线差异：Maya 的优势常常体现于角色和成熟动画流程，Houdini 的优势集中在程序化、FX 和场景组装，Blender 则以综合能力和较低许可门槛吸引小中型团队。

Maya 和 Blender 的比较也不应只看“谁的建模功能多”。真正影响团队决策的往往是资产、绑定和动画能否顺利交接，以及许可、培训、供应商和既有工具链的成本。

## 七、同一个带孔零件的五种答案

假设我们有一个带孔的支架或外壳。

### CAD 的答案

孔是一个有直径、深度、位置、基准和公差的设计特征。它可能来自草图约束、拉伸切除或孔向导，并被装配、工程图和制造标注引用。

### CAE 的答案

如果孔远离关注区域，仿真工程师可能把它简化；如果孔影响应力集中，则需要保留并在周围细化网格。CAE 关心的是孔对物理结果的影响，而不是完整复刻每一个 CAD 特征。

### CAM 的答案

孔需要对应刀具、加工顺序、进给、转速、深度、冷却和避让动作。CAM 输出的是机床中心线轨迹和后处理代码，而不是一颗“更漂亮的孔”。

### BIM 的答案

BIM 不会把这个对象优先理解成机械零件特征，而会把它放入建筑构件、设备、连接件或族的语义体系中。它关心对象属于谁、具有什么属性、与哪些构件相连以及能否被统计和协调。

### DCC 的答案

DCC 首先关心镜头里孔是否正确、边缘是否需要支撑线、材质和法线是否正常、模型是否会在变形和渲染时出问题。孔的视觉效果可能比它是否满足某个制造公差更重要。

五个答案都可能“看起来像同一个孔”，但它们的参数、数据结构和下游用途完全不同。

## 八、互操作：格式也有自己的行业边界

### STEP：面向机械产品数据

STEP 是 ISO 10303 系列标准，用于异构 CAD 系统之间交换产品模型数据。STEP AP242 关注精确 B-Rep、装配结构、产品元数据和语义 PMI/GD&T。

它适合 CAD 到 CAD 的供应商交换和长期归档，但不能保证完整保留原软件的特征树、约束历史和所有设计意图。

### IFC：面向建筑信息交换

IFC 是 buildingSMART 面向 AEC 行业的开放中立标准。它不仅描述几何，也描述建筑空间结构、构件对象、属性集合、数量和关系。

STEP AP242 与 IFC 不是谁取代谁的关系。前者主要面向机械产品模型和 PMI，后者主要面向建筑、基础设施、机电和设施管理信息。把机械 CAD 数据直接塞进 IFC，或把 BIM 模型当成普通网格导出，都会损失语义。

### OBJ、FBX、glTF 和 USD：面向网格与内容管线

OBJ 适合静态网格，FBX 常用于带材质、骨骼和动画的 DCC 交接，glTF/GLB 适合 Web、AR 和实时预览，USD 更强调场景组合、层级、资产引用和大型影视/仿真管线。

这些格式可以帮助资产跨工具流动，但不等于 CAD 的 B-Rep、BIM 的族关系或 CAM 的制造意图都能被无损翻译。

## 九、五类软件如何判断“模型好不好”

| 类别 | 合格标准 | 常见失败 |
|---|---|---|
| CAD | 几何精度、约束稳定、特征可编辑、可制造 | 特征树崩溃、拓扑无效、不可制造 |
| CAE | 网格质量、边界条件合理、结果收敛、可解释 | 网格失败、过度简化、边界条件错误 |
| CAM | 可加工、无碰撞、机器可执行、节拍和成本合理 | 过切、干涉、后处理错误、效率低 |
| BIM | 对象语义、属性、关系、协调和信息连续 | 只有几何、属性丢失、映射不一致 |
| DCC | 拓扑、UV、材质、绑定、变形、渲染和交接 | 面翻转、UV 重叠、变形塌陷、格式不兼容 |

## 十、结语：选择建模软件，先选择交付物

如果你的交付物是精确零件、工程图和供应商数据，优先考虑 CAD；如果交付物是物理验证结果，重点是 CAE 的前处理和求解链；如果交付物是机床能执行的程序，重点是 CAM；如果交付物是建筑构件、工程量和运维信息，重点是 BIM；如果交付物是角色、环境、动画或实时资产，重点是 DCC。

“哪个软件建模更强”通常不是一个有意义的问题。更有意义的问题是：

- 你的模型需要被谁继续使用？
- 它需要保留几何、设计意图、物理属性、制造过程还是视觉管线？
- 下一个软件要读取什么？
- 最终交付物是图纸、报告、刀路、建筑数据库，还是能被观看和动画化的资产？

只要先回答这几个问题，软件选择就会从品牌比较，变成可验证的数据和流程决策。

## 参考素材

6. 技术栈，3D 建模入坑指南：NURBS 与 Polygon / CAD 与 DCC 怎么选  
   https://jishuzhan.net/article/2037985489836376065

14. DemystifyingPLM，CAD Modeling Paradigms: NURBS, Parametric, Implicit, SubD  
    https://www.demystifyingplm.com/cad-modeling-paradigms-nurbs-parametric-implicit

15. Shapr3D，The Technological Foundations of CAD Software  
    https://www.shapr3d.com/content-library/what-is-cad-the-technological-foundations-of-cad-software

20. Cadence，Using NURBS vs. Mesh: Why Both Are Needed  
    https://resources.system-analysis.cadence.com/blog/msa2022-using-nurbs-vs-mesh-why-both-are-needed

31. NovaSolver，CAE Analysis Workflow  
    https://novasolver.jp/en/guide/workflow.html

34. Spatial，FEM Preprocessing is the Bottleneck  
    https://blog.spatial.com/fem-preprocessing-is-the-bottleneck

35. CAD Interop，CADfix CAE: CAD Model Preparation for Simulation  
    https://cadinterop.com/en/our-products/cadfix/cad-reuse-for-cae.html

36. DemystifyingPLM，CAD vs CAM  
    https://www.demystifyingplm.com/cad-vs-cam

38. UTEC，CAD/CAM Workflow for CNC Machining  
    https://resources.utec.co/cad-cam/cad-cam-workflow-cnc-machining/

43. 建筑信息模型（BIM）三维建模软件的应用比较  
    http://tmjzgcxxjs.manuscripts.cn/cn/article/id/e2ba281a-f1f9-44cd-ad2f-79472e49a193

45. Augmintech，Revit Modeling Explained  
    https://augmintech.com/revit-modeling

46. TheFuture3D，BIM vs CAD  
    https://thefuture3d.com/learn/bim-vs-cad/

53. CG Lounge，VFX Software in 2026  
    https://cglounge.studio/journal/the-honest-map-to-dcc-software

54. Alignify，Best 3D Modeling Tools: CAD vs DCC  
    https://alignify.co/tools/3d-modelling

56. Threedium，3D Modeling Workflows  
    https://threedium.io/3d-model/modeling-workflows

57. NastyRodent，Maya vs Blender for Production Pipelines  
    https://nastyrodent.com/maya-vs-blender-for-production-pipelines/

61. PhotoLib，3D File Formats Explained  
    https://photolibsoftware.com/guides/3d-file-formats-explained/

63. CAD Interop，STEP AP242  
    https://www.cadinterop.com/en/formats/neutral-format/step.html

64. CAD Interop，IFC Format  
    https://www.cadinterop.com/en/formats/neutral-format/ifc.html