### @AutoWired

支持根据id去找寻，支持required=false

### @Resource

不支持required=false，不支持@Primary 按名称查找的，支持name=“”查找，属于JSR250规范

### @Inject

支持@Primary 不支持required=false 需要导入javax.inject包，属于JSR330规范

### @Primary

多个bean存在，查询当前bean，与@Autowired使用，@Inject可以一起使用

### @Qualifier

支持按名称查询，name=“”



