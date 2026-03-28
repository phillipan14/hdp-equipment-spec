# 与庄梓文对话笔记 — Conversation Notes with Ziwen Zhuang
**Date:** ~March 2026 | **Location:** Tsinghua University area, Beijing
**Participants:** Phillip An, Ziwen Zhuang (PhD researcher, legged locomotics / humanoid robotics)

---

## Part 1 (~23 min) — Research Background & Technical Landscape

### 庄梓文的研究方向 / Ziwen's Research Focus

**Phillip:** 所以你现在那个研究是做的是什么的？
> *So what's your research on right now?*

**Ziwen:** 大部分就是一直是做足式（legged locomotion）的。就是在AI之前，大家对机器人智能的理解只有导航。现在有了大模型，大家可能想的更多一些 —— 全身控制，或者是跟语言、行为这些东西结合上。可能后面也在往这个方向走。

> *Mostly legged locomotion. Before AI, everyone's understanding of robot intelligence was just navigation. Now with foundation models, people are thinking about whole-body control, or combining with language and behavior. That's probably the direction things are going.*

**Context:** Ziwen is a PhD researcher focused on legged robot locomotion, working at the intersection of reinforcement learning and sim-to-real transfer. His work involves training locomotion policies in simulation (MuJoCo/Isaac Sim) and deploying them on real humanoid hardware.

---

### 人形机器人的形态优势 / Humanoid Form Factor Advantages

**Ziwen:** 比如说人形 —— 就是这个形态它足够通用。它的手脚并用其实非常直观的运动。那这个时候你短期的路过冒险（locomotion）可能做会谈个机会。它当那大型部分机械上，我可以做到说我可以钻到桌子底下。反正原来传统的机器人吃不了的。

> *For example, humanoids — this form factor is versatile enough. Using hands and feet together is very intuitive movement. With locomotion, there's opportunity. For larger scale machinery, I can get under a table. Traditional robots can't do that.*

**Ziwen:** 但是现在这种人形还不太行。他们都以为它可以做的事情多样性太多了。但我可能约束说你必须要做什么事，它可能啥都做，但是做出来不是你想要的。

> *But current humanoids aren't quite there yet. People think they can do too many diverse things. If I constrain what you must do, it might do everything, but the output isn't what you want.*

---

### 四足机器人 vs 人形 / Quadruped vs Humanoid

**Ziwen:** 四足基本上一动嘛，这件事已经基本解决了。

> *Quadruped locomotion is basically a solved problem.*

**Phillip:** 所以你现在这个大模型的部分是哪个方面的？偏基础的问题？

**Ziwen:** 偏后端比较多。比如说执行人（executor）。

> *More on the backend. Like the executor / policy side.*

**[Research Context]:** The field distinguishes between "high-level" (task planning, language understanding) and "low-level" (motor control, locomotion) in humanoid robotics. Ziwen focuses on the low-level control policies — how to translate high-level commands into actual joint movements. This is critical for data collection because the quality of teleop data depends on how well the robot can execute commanded trajectories.

---

### Locomotion 技术细节 / Reference Trajectory Tracking

**Ziwen:** 它不需要知道前面是什么地形。它只需要知道它离当前位置现在的偏移了多少。因为它还是个跟踪（tracking）的问题。在运行的过程中，我告诉一个当前需要做的动作，然后这个神经网络只会去执行这一串动作。因为它只需要知道我在实际做到和目标的偏差是多少，我偏多少。我只要告诉它，它就可以自己去修正。

> *It doesn't need to know what terrain is ahead. It only needs to know how far it's deviated from its current position. Because it's still a tracking problem. During runtime, I tell it the current desired motion, and the neural network just executes that sequence. Because it only needs to know the deviation between where I actually am and where I should be. As long as I tell it that, it can self-correct.*

**Ziwen:** 比如说我要上台，它不需要知道前面是个台子。它只需要知道我离这个目标点有多远。它会给一个 reference trajectory 进来，这个 reference trajectory 会告诉它你现在应该做什么动作，比如说你现在应该起跳。但是因为它之前在环境里面训练的历史，都是起跳的时候前面有个台子。

> *For example, if I need to get on a platform, it doesn't need to know there's a platform ahead. It just needs to know how far I am from the target point. A reference trajectory comes in and tells it what action to do — like "jump now." Because in its training history in simulation, jumping always meant there was a platform in front.*

**[Technical Context]:** This describes a **reference trajectory tracking** approach to locomotion — the robot follows a pre-computed motion plan while a learned policy handles real-time corrections for terrain irregularities. This is more robust than end-to-end learning because it decomposes the problem: a planner generates ideal trajectories, and a low-level controller tracks them. Companies like Boston Dynamics, Agility Robotics, and Unitree all use variations of this approach.

---

### 仿真训练 / Simulation Training (MuJoCo, Isaac Sim)

**Ziwen:** 这些大部分都是用 simulation 做的。基本上全都是。

> *Most of this is done in simulation. Basically all of it.*

**Phillip:** 用的什么？MuJoCo？

**Ziwen:** 它就用法不一样。比如说 MuJoCo 还是比较软的（soft contact）。比如说我那个跑步，要跑真实世界的地形，这也是比较弱的。只是用法跟那个 Isaac Sim 那种不一样。

> *The usage is different. MuJoCo has softer contact modeling. For my running work on real-world terrain, it's also weaker. The usage differs from Isaac Sim.*

**[Research Context]:**
- **MuJoCo** (Multi-Joint dynamics with Contact): DeepMind's physics engine, widely used in RL research. Good for soft contact but less accurate for rigid impact.
- **Isaac Sim/Gym** (NVIDIA): GPU-accelerated simulation, better for large-scale parallel training. Used by most production humanoid companies.
- **Sim-to-real gap** remains the biggest challenge — policies that work perfectly in simulation often fail on real hardware due to differences in friction, contact dynamics, latency, and sensor noise.

---

### Boston Dynamics & Lab Infrastructure

**Phillip:** 这 M1（MIT?）总是做的？

**Ziwen:** M1 总是自己有个厂房。我没厂房，我跟一个小组。我已经有看见那个 Boston Dynamics 上面有一个交区有一个很大厂房。

> *M1/MIT always has their own facility. I don't have one, I work with a small group. I've seen Boston Dynamics has a huge facility in the suburbs.*

**Ziwen:** 机器坏。还是挺坏机器坏的。所以大部分都是用仿真来训练。有很多工具可以直接在仿真里训练的。

> *Robots break. They really do break a lot. So most training is in simulation. There are lots of tools for training directly in simulation.*

**[Context]:** This validates a key HDP insight — real robot hardware is expensive and fragile. Simulation is the primary training method, but **real-world data** (especially manipulation/tactile data) remains the critical bottleneck that simulation cannot fully replace. This is exactly the gap HDP aims to fill.

---

### 1X (formerly Halodi Robotics)

**Ziwen:** 欧洲有一个公司叫 1X。

**Phillip:** 1X，你听过吗？

**Ziwen:** 上个月还是半个月前，不是在清华差不多跟几个人都一起聊了。

> *About a month or half a month ago, they were at Tsinghua chatting with a few people.*

**Phillip:** 要我在 X（Twitter）上刚刚看见他。

**[Company Profile — 1X (formerly Halodi Robotics)]:**
- **HQ:** Moss, Norway (with US operations in Sunnyvale, CA)
- **Product:** NEO humanoid robot — bipedal, designed for home/commercial use
- **Funding:** $100M Series B (Jan 2024, led by EQT Ventures). Reports of $1B raise at $10B valuation. Investors: OpenAI Startup Fund, Samsung NEXT, Tiger Global
- **Previous name:** Halodi Robotics (rebranded to 1X in 2022)
- **Key:** NEO pre-orders opened Oct 2025 for 2026 delivery. Price: $20K purchase or $499/month subscription. Available in tan, gray, dark brown. Focus on private home deployment.
- **CEO:** Bernt Øivind Børnich

---

### 数据工厂概念 / Data Factory Concept

**Phillip:** 所以我们现在想的就是做这种的，最基本的这个需求就是给机器人公司做一个 Data Factory。

> *So what we're thinking is to build the most basic version of this — a Data Factory for robotics companies.*

**Ziwen:** 机器人现在有一个问题。Data Factory —— 一方面国内的，政府都在搞。

> *Robots have a problem right now. Data Factory — on one hand, the Chinese government is already doing this.*

**Phillip:** 美国的公司就没有。

> *American companies don't have this.*

**Ziwen:** 对，清华建了那个。但是那个 Data 能不能用，不知道。而且那些数据不是不会卖给国外公司。

> *Right, Tsinghua built one. But whether that data is usable, who knows. And that data won't be sold to foreign companies.*

**[Strategic Context]:** This validates HDP's core thesis:
1. **China has data collection infrastructure** (government-funded, university labs) but the data stays in China
2. **US companies are data-starved** — they have capital but no large-scale data collection operations
3. **There's a supply-demand mismatch** that HDP can bridge by operating in a neutral location (SE Asia)

---

### 触觉数据缺口 / Tactile Data Gap

**Ziwen:** 我跟一个教授聊的，他说大部分的一些 Data 都用不了。因为他的那个 distribution 就是 —— 百分之七十是 happy path（成功路径），就是那个 tactile data。然后他也说应该是那个 Microsoft 用这个。

> *I chatted with a professor who said most of the data is unusable. Because the distribution is — 70% is happy path, meaning the tactile data. And he said Microsoft is using this.*

**Phillip:** 这其实他在那个 SWC（Schwarzman College）他也在做 Dex（dexterous manipulation）。

**[Research Context]:**
- **Tactile data** is the most critical and hardest-to-collect modality for robotic manipulation
- Current datasets are heavily biased toward "happy path" (successful grasps) with insufficient failure cases
- The professor referenced is likely **赵唐 (Zhao Tang)**, an angel investor in robotics data who has spoken about this gap
- Microsoft Research has invested heavily in tactile sensing for dexterous manipulation (Project Dex)
- This reinforces HDP's strategy to include **tactile gloves** in higher-tier data collection setups

---

## Part 2 (~46 min) — Industry Landscape, Business Strategy & Opportunities

### GTC & Figure AI Demo Quality

**Ziwen:** GTC 上面他们只有油掌势（demo videos only），所以大家就说比较有一个什么问题。

> *At GTC they only showed video demos, so people are questioning the authenticity.*

**Phillip:** 比如说 Figure AI，大家也有很多怀疑，他拍的视频太过完美。

**Ziwen:** Figure 以前他们都想做那个在工厂的嘛，在 BMW 到处有大客户。但是现在他们都是在那个家里做的。后来说 BMW 到处有就那三台机器，而且根本就是假的，是跟方的不动啊。

> *Figure used to want to do factory work, had BMW as a big client. But now they're all doing home tasks. Later it came out that the BMW deployment was just 3 machines, and they were basically fake — just standing there not moving.*

**[Company Profile — Figure AI]:**
- **HQ:** Sunnyvale, CA
- **Valuation:** $39B post-money (Series C, Sept 2025)
- **Funding:** ~$1.9B total. Series C: $1B+ led by Parkway Venture Capital. Investors: Brookfield Asset Management, NVIDIA, Macquarie Capital, Intel Capital, Salesforce, T-Mobile Ventures, Qualcomm Ventures, Microsoft, OpenAI, Jeff Bezos
- **Key products:** Figure 01, Figure 02, Figure 03 (3rd-gen, named TIME best invention 2025)
- **BMW partnership:** Announced 2024, but actual deployment reportedly just 3 units standing idle
- **Project Go-Big:** Partnership with Brookfield to build world's largest humanoid pretraining dataset using 100% egocentric human video data collected passively in real homes. Access to Brookfield's 100K+ residential units, 500M sq ft commercial, 160M sq ft logistics
- **White House:** Demonstrated for Trump administration
- **Criticism:** High valuation relative to actual deployed units; demo videos questioned for authenticity

---

### 1X Neo — Pricing & Market

**Phillip:** 1X 的 Neo 已经开始卖了还是？

**Ziwen:** 一个月是 500 块钱，然后一个整机两万块钱。

> *$500/month lease, or $20,000 to purchase outright.*

**Ziwen:** 然后它就想开放这种测试，让大家训练。

> *They want to open up testing access so people can train on it.*

**[Context]:** 1X's pricing strategy is aggressive — at $500/month, they're targeting a consumer/prosumer market. For HDP, this means 1X robots could potentially be part of a later-phase deployment where operators use commercial humanoids as the teleop platform. The $20K price point is comparable to high-end ALOHA setups.

---

### Scale AI — Revenue Benchmark

**Ziwen:** 你知道他们（Scale AI）的 revenue？一年是四亿美金。

> *You know their (Scale AI's) revenue? $400 million a year.*

**Ziwen:** 他们以前是纯软件。但现在越来越多的就是机器人的 training 的。他们现在还是一个轻资产模式的。

> *They used to be pure software. But now more and more is robotics training. They're still an asset-light model.*

**[Company Profile — Scale AI]:**
- **HQ:** San Francisco
- **Valuation:** ~$29B (2025, after Meta's $14.3B investment for 49% non-voting stake). Exploring tender offer at $25B in 2026
- **Revenue:** ~$870M (2024), projected 130% growth to ~$2B by 2026. Ziwen quoted $400M which may reflect an earlier period or net revenue
- **Business model:** Data labeling platform, expanding into robotics training data, government/defense contracts
- **Relevance to HDP:** Scale AI proves the TAM for data services at massive scale. They're the closest market comp for what HDP wants to build, but for robotics physical data rather than text/image labeling. If Scale AI can reach $2B/yr revenue, robotics data (harder to collect, more valuable per hour) has significant upside potential.

---

### AgiBot (智元机器人) & Chinese Robotics IPO Wave

**Ziwen:** 国内最早的23年24年那一波的公司 —— AGIBOT。这是清华出来的。都是自己做的也是几百亿人民币。但是他们现在都要上市了。

> *The earliest Chinese companies from 2023-2024 wave — AGIBOT. From Tsinghua. All self-built, valued at hundreds of billions RMB. But they're all going public now.*

**Ziwen:** 但是他们其实也没卖多少，也没产生具体的规模化价值。他们其实属于技术上面没有掉队，但技术边界也没突破。真的用到那个地方还不行。

> *But they haven't really sold much, haven't generated concrete scalable value. Technically they haven't fallen behind, but they haven't broken through the technical frontier either. They can't really be deployed yet.*

**[Company Profile — AgiBot (智元机器人)]:**
- **HQ:** Shanghai, China. Founded 2023 by former Huawei engineers Deng Taihua and Peng Zhihui
- **Valuation:** $1B+ → targeting HK$40-50B ($5.1-6.4B USD) in IPO
- **Products:** AgiBot A2 humanoid, various industrial robots. Led global humanoid shipments in 2025 with 5,168 units
- **IPO:** Hong Kong listing planned Q3 2026. Joint sponsors: CICC, CITIC Securities, Morgan Stanley. Plans to sell 15-25% of shares, potentially raising $1B+
- **Investors:** Tencent, HongShan Capital (formerly Sequoia China), BYD, Hillhouse, LG Electronics, Mirae Asset
- **Key insight from Ziwen:** Despite high valuations, these companies lack real revenue and scalable deployments. President Xi Jinping personally inspected their robots during a Shanghai visit.

---

### Deep Robotics (绝影/深度机器人)

**Ziwen:** 我觉得那个 Deep Robotics 就是他们做的那个四足机器人，我觉得那个挺好的。他们就有不少这样的验证。

> *I think Deep Robotics and their quadruped is pretty good. They have quite a few validations.*

**Ziwen:** 但是就很困，其实普通的四足算是可以跟那个商用了。基本上工业上在做制造上的，这让它比较有用一点点。但这就跟 AI 的方法没关系了，可能是制造业的事情。

> *But it's tough. Regular quadrupeds are commercially viable. Basically for industrial manufacturing use, they're somewhat useful. But that's not really about AI methods, it's more about manufacturing.*

**[Company Profile — Deep Robotics (深度机器人/绝影)]:**
- **HQ:** Hangzhou, China
- **Products:** Lite3, X30 quadruped robots
- **Revenue:** One of the few Chinese robotics companies with actual commercial sales
- **Applications:** Industrial inspection, power grid maintenance, mining, firefighting
- **Key insight:** Quadruped robots have found real commercial use cases (inspection, patrol) that humanoids have not yet achieved

---

### Vietnam / SE Asia Robotics Opportunity

**Phillip:** 我觉得也是一个很好的机会，就是如果你可以去这种的像越南和印度的，或者新加坡这种的小国家。然后他们现在政府也有很多这种投资。

> *I think there's also a good opportunity if you go to places like Vietnam, India, or small countries like Singapore. And their governments are making lots of investments now.*

**Ziwen:** 也有一个机器人公司，VinAI/BIN。那个人太厉害了，他太有那个...做这个 巨额投资的那个公司的 CEO。

> *There's also a robotics company, VinAI/BIN. That person is really impressive, he's the CEO of a massively funded company.*

**[Company Profile — VinAI / US-1 Robotics]:**
- **Connection:** VinAI is the AI research lab of Vingroup (Vietnam's largest conglomerate, also behind VinFast EVs)
- **US-1:** Humanoid robotics project, demonstrated at CES (3 consecutive years)
- **Founder:** Vietnamese origin, CMU PhD background
- **Key fact:** Despite being based in Vietnam, assembled in Đà Nẵng (Da Nang), components from Shenzhen
- **CES presence:** Multiple years of demos, relatively mature for a Southeast Asian robotics company

---

### 美国 vs 中国估值差距 / US-China Valuation Arbitrage

**Phillip:** 其实那个美国和中国的机器人的估值差距太大。美国比中国的十倍。

> *The valuation gap between US and Chinese robotics companies is huge. US is 10x China.*

**Ziwen:** 对。很多那个 VC 都这样。所以很多（中国公司）想在美国注册。

> *Right. Lots of VCs think this way. So many Chinese companies want to register in the US.*

**Phillip:** 这也是我们可能会考虑的。就是 position yourself as a 美国的公司。

> *This is something we might consider too. Positioning yourself as a US company.*

**[Market Context]:**
- **Valuation comparison (2026):**
  - Figure AI (US): ~$40B valuation, 0 revenue
  - Unitree (China): ~$3.8B valuation, actual product sales
  - AgiBot (China): ~$1.5B valuation
  - The 10x gap between US and China robotics valuations is real and represents an **arbitrage opportunity**
- **Explanation:** US VCs price in optionality and TAM aggressively; Chinese markets are more conservative and demand revenue
- **HDP angle:** A US-incorporated company operating data collection in SE Asia captures US valuations while leveraging Asian labor costs

---

### Figure AI — Data Demand Signal

**Phillip:** 我们有个 operator 跟 Figure 说话，他们说基本上明年底要 several million hours 的 data。然后他们就说如果你现在有 data 我们就买。

> *We have an operator who talked to Figure, they said by end of next year they need several million hours of data. They said if you have data right now, they'll buy it.*

**[Critical Business Intelligence]:**
- **Demand signal:** Figure AI has explicitly stated they need **millions of hours** of teleoperation data
- **Timeline:** End of 2027 (from "next year" relative to conversation date)
- **Willingness to pay:** They indicated readiness to purchase existing data immediately
- **Validation:** This is the strongest demand signal for HDP's business model
- **Comparison:** Scale AI built a $14B company on text/image data; robotics data is harder to collect and arguably more valuable per hour

---

### Data Quality Challenges

**Ziwen:** 数据量还有数据对齐的问题。他们当时好像是用的 MATIC 的，花了很多钱。整完的数据，还没看出效果。这时候大家开始想，不知道是方法的问题还是什么问题。因为他们的数据量体是很多的。

> *There are data volume and data alignment problems. They apparently used MATIC (motion capture), spent a lot of money. After all that data collection, they still didn't see results. So people started questioning whether it's a methods problem or what. Because their data volume was actually large.*

**Ziwen:** 大量的数据，有方法的问题。

> *Lots of data, but there are method problems.*

**[Research Context]:**
- **Data alignment** (时间对齐) is critical — multi-modal data (vision, force, joint angles) must be precisely synchronized
- **MATIC** likely refers to motion capture-based data collection (expensive, ~$50-100K+ for a full mocap setup)
- **Key insight:** Raw data volume alone doesn't solve the problem. You need:
  1. Proper temporal alignment across modalities
  2. Sufficient distribution coverage (not just happy path)
  3. Matched embodiment (data from one robot may not transfer to another)
  4. Quality filtering and annotation
- **HDP implication:** This reinforces that HDP's value proposition isn't just "collect more data" but "collect *quality* data with proper synchronization, calibration, and metadata" — exactly what the sysID and latency matching infrastructure enables

---

### 科大讯飞 (iFlytek) Humanoid Story

**Ziwen:** 当时科大讯飞，他们做的人形。派了三个工程师做了...然后做完了。

> *At the time, iFlytek was building a humanoid. They sent 3 engineers to build it... and they finished it.*

**Phillip:** 然后现在 AgiBot 也是 1.2 billion（估值）。

**Ziwen:** 其实美国和中国的机器人的估值差距太大。美国比中国的十倍。

> *The valuation gap between US and Chinese robotics companies is huge. US is 10x China.*

**[Context]:** iFlytek (科大讯飞) is China's largest AI voice technology company. Their foray into humanoid robotics with just 3 engineers highlights both the accessibility of the hardware (open-source designs like ALOHA/Koch) and the gap between building a prototype and achieving reliable deployment.

---

### YC & Robotics Data Companies

**Phillip:** 现在 YC 我以前的 batch，然后一个投资人就是 YC 合伙人。所以这次 YC 有四个新的公司也是这种的，就是 collect robotics data。

> *In YC, my previous batch, one of the partners... so this batch YC has 4 new companies also doing this — collecting robotics data.*

**[Market Signal]:**
- **YC W26 batch** reportedly has 4+ companies focused on robotics data collection
- This signals that the robotics data market is reaching a tipping point
- **Competitive landscape:** More startups entering means more validation but also more competition
- **HDP differentiation:** Scale (300-450 operators), SE Asia cost advantage, and multi-tier data quality (from phone capture to full teleop)

---

### Investment Landscape — Arms Race Dynamics

**Phillip:** 我觉得美国的想法现在跟 LLM 的公司一模一样。因为现在在美国 LLM 的公司，像有那个 Ilya 开了一个新公司 SSI（Safe Superintelligence），还有一个 Skild AI，还有 Baby（可能指某公司）。You raise one billion dollars，他们全都没有产品。

> *I think the US approach is exactly like LLM companies now. In the US, LLM companies like Ilya's SSI, Skild AI, etc. They raise a billion dollars with no products.*

**Ziwen:** 这也是 —— 像一个 Arms Race。如果你 raise money，这个 money 会帮你买 resources，帮你买 compute。

> *It's like an Arms Race. If you raise money, that money buys you resources, buys you compute.*

**[Company Profiles]:**
- **SSI (Safe Superintelligence Inc):** Ilya Sutskever's post-OpenAI company, raised $1B+ with no product
- **Skild AI:** CMU-founded, raised $300M+ for "foundation model for robots," no commercial deployment
- **Arms Race dynamic:** In both LLM and robotics, capital itself is the competitive moat — more money = more compute/data = better models = more funding. This favors US-incorporated entities that can access US capital markets.

---

### Faraday Future (FF) — Cautionary Tale

**Phillip:** FF（Faraday Future），贾跃亭的那个车。他之前是乐视的。后来在美国创业，做了个车 FF91。

> *FF (Faraday Future), Jia Yueting's car. He was previously at LeEco. Then started a company in the US, made the FF91.*

**Ziwen:** 他连量产都没。Market Cap 是 502 Million。上市那天股票是 16 万（$160K/share），现在是 0.26。

> *They never even mass produced. Market cap is $502M. Stock was $160K/share on listing day, now it's $0.26.*

**[Context — Faraday Future (FF)]:**
- **Ticker:** FFIE (NASDAQ)
- **Founded:** 2014 by Jia Yueting (贾跃亭), former LeEco CEO
- **History:** Never achieved mass production of FF91, constant cash crunches
- **Current:** Market cap collapsed 99.9%+ from peak, now working with a Chinese partner (likely Geely/ECARX subsidiary) to contract manufacture
- **Lesson for HDP:** Don't raise massive capital without a clear path to revenue. Build product-market fit first, then scale.

---

### Shenzhen Manufacturing Ecosystem

**Ziwen:** 比如说在硅谷注册的公司，用个美国名字。然后还是用的国内这些公司的生产。换一个 ID，换一个外观。

> *Like companies registered in Silicon Valley, using an American name. But still using Chinese companies' manufacturing. Just swap the ID, swap the appearance.*

**Phillip:** 这就跟那个一样。换个颜色。

> *Same thing. Just change the color.*

**[Manufacturing Context]:**
- **Shenzhen ecosystem** remains the world's dominant robotics hardware manufacturing hub
- Most US robotics companies (including Figure AI, Agility) source significant components from China
- **HDP manufacturing strategy:** Source equipment from Shenzhen suppliers (as discussed with Mark, the Beijing hardware integrator), assemble in SE Asia
- **IP consideration:** US-China trade tensions mean robotics data collected in a neutral SE Asian country avoids both Chinese data export restrictions and US import concerns

---

### AR眼镜用于数据采集 / AR Glasses for Data Collection

**Phillip:** Xreal 合作的。这双（AR眼镜）是 A2 系列，两边都有摄像头，然后也有当时定位。就是反正手的数据也能录嘛。

> *Xreal partnership. These AR glasses are A2 series, cameras on both sides, with spatial positioning. So hand data can be captured too.*

**Ziwen:** 这时候感觉真的只要一个三千块钱的设备，就是这些公司的机会。

> *At this point, it feels like you just need a $3,000 device, and that's the opportunity for these companies.*

**[Portable Data Collection Context]:**
- **XREAL** (formerly Nreal): Chinese AR glasses company, Air 2 series ~$400-500
- **Use case:** Wearable first-person data collection — operator wears AR glasses with cameras, captures egocentric video + hand tracking
- **Price point:** ~$2,000-3,000 for a complete portable kit (AR glasses + depth sensor + compute module)
- **HDP Tier 5b alignment:** This maps directly to **Level 1 (Phone-Based)** and **Level 2 (Wearable Rig)** in the portable/wearable section of the equipment spec
- **Key insight from Ziwen:** The barrier to entry for basic data collection is dropping rapidly. A $3K kit can capture useful training data.

---

### 整体市场观察 / Overall Market Observations

**Ziwen:** 我觉得现在这个领域就是 —— 如果你可以在中国做的优势的地方就是 manufacturing。或者怎么能把一个像个 robot 做一个 really cool demo，然后在美国就是 capital 和 VC。

> *I think in this field right now — if you can leverage China's advantage in manufacturing, or make a really cool robot demo, and then in the US it's about capital and VCs.*

**Phillip:** 因为那个优势不光是现在的 AgiBot 差不多是 1.6B（估值）。赶紧去上市。

> *Because the advantage is — AgiBot is at about $1.6B valuation. Rushing to go public.*

**Ziwen:** 我先把技术做扎实。比如说交付的东西它就是真的。然后至于商业上怎么落地，就得跟商务的人合作，商务的人去找。

> *I want to first make the technology solid. Like what I deliver should be real. As for how to commercialize, you need to work with business people to figure that out.*

---

### Faraday Future — White-Labeled AgiBot Robots
**[Updated Research Context]:** FF's "robotics pivot" (Feb 2026) involves selling white-labeled AgiBot A2/X2 robots under the FF brand — FF Futurist ($34,990), FF Master ($19,990), FX Aegis quadruped ($2,499). First U.S. deployment to an Airbnb operator. This validates the "register in US, manufacture in China" playbook that Ziwen described.

---

### Figure AI — Project Go-Big (Competitive Intelligence for HDP)

**[Critical Research Finding]:** Figure AI's "Project Go-Big" with Brookfield is the most significant competitive development for HDP:
- **What:** Collecting 100% egocentric human video data passively in real Brookfield properties (100K+ residential units, 500M sq ft commercial, 160M sq ft logistics)
- **How:** Humans wear cameras in homes/offices, no robot demonstrations needed. Trains Figure's "Helix" AI to translate human navigation into robot control
- **Result:** First humanoid to learn end-to-end navigation from human video alone — language commands like "go to the fridge" directly translated to SE(2) velocity commands

**HDP Competitive Analysis:**
- Project Go-Big focuses on **navigation data** (egocentric video in homes/offices)
- HDP focuses on **manipulation data** (hands-on task execution, force/torque, tactile)
- These are **complementary, not competitive** — navigation is a solved-ish problem; manipulation is where the real data gap exists
- **HDP advantage:** Our multi-modal data (tactile, F/T, joint states) captures information that passive video cannot
- **Risk:** If Figure proves that video-only data is sufficient for manipulation too, HDP's hardware-heavy approach loses value. Monitor closely.

---

## Key Takeaways & Action Items

### 1. Data is the Bottleneck (Not Algorithms)
- Every major robotics company (Figure AI, 1X, AgiBot) needs orders of magnitude more data
- Current government/university data collection in China produces data that (a) often isn't usable and (b) can't be exported
- **HDP fills this gap** by collecting export-friendly data in SE Asia

### 2. Tactile Data is the Biggest Gap
- ~70% of current datasets are "happy path" only
- Force/torque and tactile sensing data is severely underrepresented
- Microsoft, Tsinghua researchers, and others are actively seeking better tactile data
- **HDP action:** Prioritize tactile glove integration (Tier 2+) from early deployments

### 3. US-China Valuation Arbitrage is Real (10x)
- Figure AI: ~$40B, no revenue → vs AgiBot: ~$1.5B, some revenue
- US VCs price optionality aggressively
- **HDP action:** Incorporate in US (Delaware C-corp via Allston Labs), operate in SE Asia, serve US customers

### 4. Scale AI is the TAM Comp
- $870M revenue (2024), projected to reach $2B by 2026 — even larger than Ziwen's $400M quote
- $29B valuation — proves that data infrastructure companies can achieve massive scale
- Robotics data is harder to collect → should command higher prices per hour
- **HDP action:** Position as "Scale AI for robotics" in investor narrative. Revenue trajectory from $870M→$2B shows the market is expanding rapidly

### 5. Figure AI Demand Signal
- Explicitly need "several million hours" of data by end of 2027
- Willing to buy existing data immediately
- **HDP action:** Prioritize data format compatibility with Figure AI's training pipeline

### 6. Hardware is Commoditized, Data is Not
- Open-source robot arms (Koch, SO-100) cost ~$500/pair
- AR glasses + cameras cost ~$2-3K
- The bottleneck isn't hardware — it's the operational infrastructure to collect quality data at scale
- **HDP action:** Start with lightweight capture (Tier 1-2) and scale operations before investing in expensive teleop rigs

### 7. Simulation is Dominant but Insufficient
- All locomotion training happens in simulation (MuJoCo, Isaac Sim)
- But manipulation and contact-rich tasks require real-world data
- The sim-to-real gap for manipulation is much larger than for locomotion
- **HDP action:** Focus data collection on manipulation tasks (the hardest to simulate)

### 8. Portable/Wearable Collection is the Near-Term Play
- $3K device can capture useful data (AR glasses + compute)
- Lower barrier to entry → faster deployment → earlier revenue
- **HDP action:** Deploy Level 1/2 portable rigs first (Tier 5b), graduate to fixed teleop stations

---

## Companies & People Referenced

| Name | Type | Key Detail |
|------|------|------------|
| Ziwen Zhuang (庄梓文) | Researcher | Legged locomotion PhD, Tsinghua-affiliated |
| Figure AI | Company (US) | $39B valuation, humanoid, needs millions of hours of data, Project Go-Big with Brookfield |
| 1X (Halodi) | Company (Norway) | NEO robot, $499/mo lease, $20K purchase, may raise $1B at $10B |
| AgiBot (智元机器人) | Company (China) | HK IPO Q3 2026 targeting $5.1-6.4B. Founded by ex-Huawei. 5,168 units shipped 2025 |
| Galbot (银河通用) | Company (China) | $3B valuation, $800M total raised, Peking University-founded. Real factory deployments |
| Deep Robotics (绝影) | Company (China) | Quadruped robots, actual commercial sales, entering IPO guidance phase |
| Unitree (宇树科技) | Company (China) | Shanghai STAR Market IPO filed March 2026, RMB 1.7B revenue 2025 (+335% YoY), 5,500 units shipped |
| Scale AI | Company (US) | ~$29B valuation, $870M→$2B revenue trajectory, expanding into robotics data |
| Skild AI | Company (US) | CMU-founded, $1.4B raised at $14B valuation (Jan 2026), foundation model for robots |
| SSI | Company (US) | Ilya Sutskever, $2B raised at $32B valuation, ~20 employees, no product |
| Astribot (星动纪元) | Company (China) | ~RMB 10B raise, corporate orders >RMB 5B, 50% overseas, Geely-backed |
| VinAI / VinMotion | Company (Vietnam) | Vingroup's robotics subsidiaries. VinMotion: humanoid robots for VinFast factories |
| XREAL | Company (China) | AR glasses, potential data collection hardware |
| Faraday Future (FF) | Company (US/China) | Pivoted to "EAI Robotics" — white-labeled AgiBot robots. $160K→$0.26/share |
| Boston Dynamics | Company (US) | Reference for lab infrastructure |
| 赵唐 (Zhao Tang?) | Professor | Angel investor, data quality expert |
| Brookfield | Company (Canada) | $1T+ AUM, partnering with Figure AI on Project Go-Big (100K+ properties for data collection) |
| Race Capital | VC | SF-based, founded by Alfred Chuang, infrastructure/blockchain focus |
| Micro1 | Company (US) | AI-powered recruitment, $500M valuation, expanding into AI training talent |

---

*Document generated from raw Whisper transcription (tiny model), cleaned and annotated with research context. Some dialogue is reconstructed from context due to transcription artifacts.*
