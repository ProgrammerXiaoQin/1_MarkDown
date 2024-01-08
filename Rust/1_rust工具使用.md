
1. 编译 `rustc main.rs`

2. Cargo构建项目
	1. 创建项目 `cargo new [项目名]`
	2. 构建项目 `cargo build` , 编译代码
		- `--release` 编译优化 , 会让代码更快 , 但编译时间更长
	3. 运行项目 `cargo run` , 编译并执行代码
	4. 检查项目 `cargo check`  , 检查代码 , 确保编译能通过 , 不会产生可执行文件