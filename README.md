import os
import json
from datetime import datetime

HTML_TEMPLATE = """<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>王修颐 - 个人简历</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Helvetica Neue', sans-serif;
            background: #f5f7fa;
            color: #1a2a3a;
            line-height: 1.6;
        }

        .lang-switch {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 1000;
            background: white;
            padding: 8px 16px;
            border-radius: 30px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            cursor: pointer;
            font-size: 14px;
            font-weight: 500;
            border: 1px solid #e2e8f0;
            transition: all 0.3s;
        }

        .lang-switch:hover {
            background: #3b82f6;
            color: white;
            border-color: #3b82f6;
        }

        .container {
            max-width: 1100px;
            margin: 0 auto;
            padding: 40px 20px;
        }

        .resume-card {
            background: white;
            border-radius: 24px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.08);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
            padding: 50px 40px;
            color: white;
        }

        .header h1 {
            font-size: 3rem;
            font-weight: 700;
            margin-bottom: 8px;
            letter-spacing: -0.5px;
        }

        .header .subtitle {
            font-size: 1.1rem;
            opacity: 0.95;
            margin-bottom: 20px;
        }

        .header .school {
            font-size: 1rem;
            opacity: 0.9;
            display: inline-block;
            background: rgba(255,255,255,0.2);
            padding: 4px 12px;
            border-radius: 20px;
        }

        .content {
            padding: 40px;
        }

        .section {
            margin-bottom: 40px;
        }

        .section-title {
            font-size: 1.5rem;
            font-weight: 600;
            color: #1e3a8a;
            border-left: 4px solid #3b82f6;
            padding-left: 16px;
            margin-bottom: 24px;
        }

        .skills-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
        }

        .skill-tag {
            background: #eff6ff;
            color: #1e40af;
            padding: 8px 20px;
            border-radius: 30px;
            font-size: 0.9rem;
            font-weight: 500;
            transition: all 0.2s;
        }

        .skill-tag:hover {
            background: #3b82f6;
            color: white;
            transform: translateY(-2px);
        }

        .timeline-item {
            margin-bottom: 28px;
            padding-left: 20px;
            border-left: 2px solid #e2e8f0;
            position: relative;
        }

        .timeline-item::before {
            content: '';
            position: absolute;
            left: -6px;
            top: 0;
            width: 10px;
            height: 10px;
            border-radius: 50%;
            background: #3b82f6;
        }

        .timeline-title {
            font-size: 1.1rem;
            font-weight: 600;
            color: #0f172a;
            margin-bottom: 6px;
        }

        .timeline-date {
            font-size: 0.85rem;
            color: #64748b;
            margin-bottom: 10px;
        }

        .timeline-desc {
            color: #334155;
            font-size: 0.95rem;
        }

        .volunteer-stats {
            background: #f8fafc;
            padding: 20px;
            border-radius: 16px;
            margin-bottom: 20px;
            border: 1px solid #e2e8f0;
        }

        .hours-badge {
            display: inline-block;
            background: #3b82f6;
            color: white;
            padding: 6px 16px;
            border-radius: 30px;
            font-weight: 600;
            font-size: 1.2rem;
            margin-bottom: 16px;
        }

        .volunteer-list {
            list-style: none;
            padding: 0;
        }

        .volunteer-list li {
            padding: 8px 0;
            padding-left: 24px;
            position: relative;
            color: #334155;
        }

        .volunteer-list li::before {
            content: '✓';
            position: absolute;
            left: 0;
            color: #3b82f6;
            font-weight: bold;
        }

        .two-columns {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
        }

        .footer {
            background: #f8fafc;
            padding: 20px 40px;
            text-align: center;
            border-top: 1px solid #e2e8f0;
            color: #64748b;
            font-size: 0.85rem;
        }

        @media (max-width: 768px) {
            .header {
                padding: 30px 20px;
            }
            .header h1 {
                font-size: 2rem;
            }
            .content {
                padding: 24px;
            }
            .two-columns {
                grid-template-columns: 1fr;
                gap: 20px;
            }
        }

        [lang="en"] {
            display: none;
        }

        body.en [lang="zh"] {
            display: none;
        }

        body.en [lang="en"] {
            display: block;
        }

        body.en .skill-tag,
        body.en .timeline-item,
        body.en .volunteer-list li,
        body.en .hours-badge {
            display: block;
        }
    </style>
</head>
<body>
    <div class="lang-switch" onclick="toggleLanguage()">
        <span lang="zh">English</span>
        <span lang="en">中文</span>
    </div>

    <div class="container">
        <div class="resume-card">
            <div class="header">
                <h1>王修颐</h1>
                <div class="subtitle">
                    <span lang="zh">华东政法大学 · 会计学系</span>
                    <span lang="en">East China University of Political Science and Law · Accounting</span>
                </div>
                <div class="school">
                    <span lang="zh">本科在读</span>
                    <span lang="en">Undergraduate Student</span>
                </div>
            </div>

            <div class="content">
                <div class="section">
                    <div class="section-title">
                        <span lang="zh">专业技能</span>
                        <span lang="en">Skills</span>
                    </div>
                    <div class="skills-grid">
                        <div class="skill-tag">英语 / English</div>
                        <div class="skill-tag">物料设计 / Material Design</div>
                        <div class="skill-tag">美术 / Fine Arts</div>
                        <div class="skill-tag">写作 / Writing</div>
                        <div class="skill-tag">AI调用 / AI Integration</div>
                    </div>
                </div>

                <div class="section">
                    <div class="section-title">
                        <span lang="zh">教育经历</span>
                        <span lang="en">Education</span>
                    </div>
                    <div class="timeline-item">
                        <div class="timeline-title">
                            <span lang="zh">华东政法大学 · 会计学系</span>
                            <span lang="en">East China University of Political Science and Law - Accounting</span>
                        </div>
                        <div class="timeline-date">2024.09 - <span lang="zh">至今</span><span lang="en">Present</span></div>
                        <div class="timeline-desc">
                            <span lang="zh">本科在读</span>
                            <span lang="en">Bachelor's Degree Candidate</span>
                        </div>
                    </div>
                    <div class="timeline-item">
                        <div class="timeline-title">
                            <span lang="zh">上海市陆行中学</span>
                            <span lang="en">Shanghai Luhang High School</span>
                        </div>
                        <div class="timeline-date">2024.06 <span lang="zh">毕业</span><span lang="en">Graduated</span></div>
                    </div>
                </div>

                <div class="section">
                    <div class="section-title">
                        <span lang="zh">个人经历</span>
                        <span lang="en">Experience</span>
                    </div>
                    <div class="timeline-item">
                        <div class="timeline-title">
                            <span lang="zh">班级团支书</span>
                            <span lang="en">Class Youth League Branch Secretary</span>
                        </div>
                        <div class="timeline-date">2024.09 - 2025.06</div>
                        <div class="timeline-desc">
                            <span lang="zh">带领班级获得"优秀团支部"称号，组织策划多项团日活动和主题班会</span>
                            <span lang="en">Led the class to win the "Excellent Youth League Branch" award, organized multiple league activities and class meetings</span>
                        </div>
                    </div>
                    <div class="timeline-item">
                        <div class="timeline-title">
                            <span lang="zh">班级副团支书</span>
                            <span lang="en">Deputy Youth League Branch Secretary</span>
                        </div>
                        <div class="timeline-date">2025.09 - <span lang="zh">至今</span><span lang="en">Present</span></div>
                        <div class="timeline-desc">
                            <span lang="zh">协助团支书开展日常工作，负责团务资料整理和团员联络</span>
                            <span lang="en">Assisted the secretary with daily operations, managed league affairs documentation and member communication</span>
                        </div>
                    </div>
                    <div class="timeline-item">
                        <div class="timeline-title">
                            <span lang="zh">华东政法大学大学生社会法律援助中心 · 新媒体副部长</span>
                            <span lang="en">ECUPL Student Legal Aid Center - Deputy Director of New Media</span>
                        </div>
                        <div class="timeline-date">2025.05 - <span lang="zh">至今</span><span lang="en">Present</span></div>
                        <div class="timeline-desc">
                            <span lang="zh">负责美工宣传工作，设计活动海报、宣传物料，运营新媒体平台，策划线上普法内容</span>
                            <span lang="en">Responsible for art and publicity work, designed event posters and promotional materials, managed new media platforms, planned online legal education content</span>
                        </div>
                    </div>
                </div>

                <div class="section">
                    <div class="section-title">
                        <span lang="zh">志愿服务</span>
                        <span lang="en">Volunteer Experience</span>
                    </div>
                    <div class="volunteer-stats">
                        <div class="hours-badge">
                            <span lang="zh">累计志愿时长 82 小时</span>
                            <span lang="en">Total Volunteer Hours: 82 Hours</span>
                        </div>
                        <ul class="volunteer-list">
                            <li>
                                <span lang="zh">巴金图书馆志愿者 · 安全维保</span>
                                <span lang="en">Bajin Library Volunteer - Safety Maintenance</span>
                            </li>
                            <li>
                                <span lang="zh">火车站春运志愿者 · 急救准备、秩序维护</span>
                                <span lang="en">Railway Station Spring Festival Volunteer - Emergency Preparedness, Order Maintenance</span>
                            </li>
                            <li>
                                <span lang="zh">小红书 Redland 志愿者 · 展会筹备、志愿者调度</span>
                                <span lang="en">Xiaohongshu Redland Volunteer - Event Preparation, Volunteer Coordination</span>
                            </li>
                            <li>
                                <span lang="zh">GREENEXT Expo 展前调度志愿者 · 展会筹备、调度工作</span>
                                <span lang="en">GREENEXT Expo Pre-Event Coordinator - Exhibition Preparation, Scheduling</span>
                            </li>
                        </ul>
                        <div class="timeline-desc" style="margin-top: 16px;">
                            <span lang="zh">工作认真负责，受到主办方和团队一致好评</span>
                            <span lang="en">Worked diligently and received high praise from organizers and team members</span>
                        </div>
                    </div>
                </div>
            </div>

            <div class="footer">
                <span lang="zh">最后更新：""" + datetime.now().strftime("%Y年%m月%d日") + """</span>
                <span lang="en">Last Updated: """ + datetime.now().strftime("%B %d, %Y") + """</span>
            </div>
        </div>
    </div>

    <script>
        function toggleLanguage() {
            document.body.classList.toggle('en');
            localStorage.setItem('language', document.body.classList.contains('en') ? 'en' : 'zh');
        }
        
        const savedLang = localStorage.getItem('language');
        if (savedLang === 'en') {
            document.body.classList.add('en');
        }
    </script>
</body>
</html>
"""

def generate_resume():
    filename = "index.html"
    with open(filename, "w", encoding="utf-8") as f:
        f.write(HTML_TEMPLATE)
    print(f"✅ 简历已生成: {filename}")
    print(f"📅 最后更新日期: {datetime.now().strftime('%Y-%m-%d')}")
    
    readme = """# 王修颐个人简历网页

简约蓝色风格的在线简历，支持中英文切换。

## 功能特点
- 中英文双语切换，自动记忆语言偏好
- 响应式设计，适配手机和电脑
- 蓝色简约风格，美观大方
- 展示教育经历、个人经历、志愿服务等信息

## 快速部署到GitHub Pages

1. 将此仓库推送到GitHub
2. 进入仓库 Settings → Pages
3. 在 Branch 选择 main 分支，点击 Save
4. 访问 `https://你的用户名.github.io/仓库名/` 即可查看简历

## 本地预览

直接在浏览器中打开 `index.html` 文件即可预览。

## 自定义修改

编辑 `index.html` 文件中的内容即可更新简历信息。

---
最后更新：""" + datetime.now().strftime("%Y-%m-%d")

    with open("README.md", "w", encoding="utf-8") as f:
        f.write(readme)
    print("✅ README.md 已生成")
    
    print("\n🎉 所有文件已准备就绪！")
    print("\n下一步：")
    print("git init")
    print("git add .")
    print("git commit -m 'Add bilingual resume'")
    print("git remote add origin https://github.com/你的用户名/仓库名.git")
    print("git push -u origin main")
    print("\n然后在 GitHub 仓库 Settings → Pages 中启用即可")

if __name__ == "__main__":
    generate_resume()# -
