# Fridge experiment

LLM 扮演 Norman 书里的冰箱：它模拟的是人们以为的冰箱，还是真实的冰箱？

- 实验设计：[DESIGN.md](DESIGN.md)

---

## Notes

**原书核实（2026-11-10）**
- **旋钮分工**：原书 Figure 1.10 的图注写明，freezer 旋钮决定冷冻室温度（温控器 / 制冷单元），refrigerator 旋钮决定冷气在两个隔间之间的分配比例（分流阀）。与 DESIGN.md 的设定一致，所以 B 组"你是 Norman 书里的冰箱"不会和提示冲突。这台冰箱是 GE 的。
- **冷热方向**：原书正文没有写明。Figure 1.9 的设置说明面板写着 "Normal C and 5"、"Colder Fresh Food C and 6-7"、"Colder Freezer D and 7-8"，说明字母越往后冷冻越冷、数字越大冷藏越冷，与我们的约定一致。
- **面板本身不完全自洽**：面板并不完全符合简单的"温控器 + 分流阀"模型（例如 "Colder Freezer" 也把数字调高了，"OFF (both)" 在数字旋钮上）。Norman 自己也写道，他无法确定哪个旋钮接温度传感器、传感器在哪个隔间。所以实验只用写进提示的规则当标准答案，不使用原书面板。这一点可以写进博客。
- **24 小时**：原书引用说明书，调整后要等 24 小时温度才稳定。这支持我们问"一天后"的温度。
- **Q5 措辞**：可直接采用 Figure 1.9 图注的问题："冷冻太冷、冷藏刚好，怎样调才能让冷冻变暖、冷藏不变？"

## References

- Norman, D. A. (2013). *The Design of Everyday Things* (Revised and expanded ed.). Basic Books. Chapter 1, Figures 1.9–1.10. [archive.org PDF](https://ia902800.us.archive.org/3/items/thedesignofeverydaythingsbydonnorman/The%20Design%20of%20Everyday%20Things%20by%20Don%20Norman.pdf)（PDF 第 44–46 页）
- Stanford CS147, Autumn 2024, Lecture 10: Conceptual Models（第 16 页：设置说明面板）. [PDF](https://hci.stanford.edu/courses/cs147/2024/au/lectures/10-conceptual-models.pdf)
- Stanford CS147, Autumn 2023, Lecture 11: Conceptual Models（第 15–16 页：Figure 1.10A/B 重画版）. [PDF](https://hci.stanford.edu/courses/cs147/2023/au/lectures/11-conceptual-models.pdf)
- Sheridan, D. (2022, March 14). Mental models with an example. *Fact of the Day*. [Link](https://www.factoftheday1.com/p/march-14-mental-models-with-an-example)（老师原文给的链接）
