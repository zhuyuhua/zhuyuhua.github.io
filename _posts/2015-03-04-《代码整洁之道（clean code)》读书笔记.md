---
layout: post
title: 《代码整洁之道（clean code)》读书笔记
date: 2015-03-04 21:00:00
comments: false
---


###第1章
 1. 代码永存
 2. 勒布朗法则：稍后等于用不
 3. 整洁的代码只做好一件事
 4. 代码：每次签入时，都比签出时干净！

###第2章 有意义的命名

 1. 名副其实
 2. 避免误导
   避免使用与本意相悖的词，如：hp，aix。
 3. 做有意义的区分
 不要以数字系列命名，不要废话，如Product和ProductInfo、ProductData。
 4. 使用读得出来的名称：不要自造词！
 5. 使用可搜索的名称
 6. 避免使用编码
 不要匈牙利语标记法、去掉成员前最，如:不要用m_前缀来标明成员变量。接口和实现：去掉接口的前缀I，宁愿选择实现是编码。
 7. 避免思维映射
 8. 类型和对象名应该是名词或者名词短语，如Customer、WikiPage，类名不能是动词。
 9. 方法名是动词或者动词短语，如postPayment。
 10. 不要使用俗语或者俚语
 11. 每个概念对应一次，整个系统应一以贯之的命名。
 12. 不用双关语
 13. 使用解决方案领域名称，即使用行业名称。
 14. 使用源自所涉及问题领域的名称。
 15. 添加有意义的语境：给名称添加前缀。
 16. 不要添加没用的语境

###第3章 函数

 1. 短小
 2. 只做一件事：函数应该做一件事，并且只做一件事。
 判断函数是否不止做了一件事的方法是：看该函数是否能再拆出一个函数，而这个函数不仅只是单纯地重新诠释其实现。
 3. 每个函数一个抽象层级
 4. switch语句
    如果出现switch，用于创建多态对象，并且隐藏在某个继承关系中，在系统其他部分看不到。示例：

        public Money calculatePay(Employee e){
        switch (e.type){
            case COMMISSIONED:
                return calculateCommissionedPay(e);
            case HOURLY:
                return calculateHourlyPay(e);
            case SALARIED:
                return calculateSalariedPay(e);
            default:
                throw new InvalidEmployeeType(e.type);
            }
        }

    重构如下：
    
        public abstract class Employee{
            public abstract boolean isPayday();
            public abstract Money calculatePay();
            public abstract void deliverPay(Money pay);
        }
        ----------
        public interface EmployeeFactory{
            public Employee makeEmployee(EmployeeRecord r);
        }
        ----------
        public class EmployeeFactoryImpl implements EmployeeFactory{
            public Employee makeEmployee(EmployeeRecord r){
                switch (r.type){
                 case COMMISSIONED:
                    return new CommissionedEmployee(r);
                case HOURLY:
                    return new HourlyEmployee(r);
                case SALARIED:
                    return new SalarieEmployee(r);
                default:
                    throw new InvalidEmployeeType(e.type);
            }
            }
        }
 5. 使用描述性的名称
 6. 函数参数：有足够的理财才使用三个或者三个以上的参数
 7. 无副作用
 8. 分隔指令和询问：函数要么做什么事，要么回答什么事，二者不可得兼（这也是命令与查询模式。例子：
        
        定义：
        public boolean set (String attribute, String value);
        使用：
        if (set("username","joe"){
        }
    这样就会产生误解：是username属性值是否之前已经被设置为joe，还是username被成功设置为joe才返回true？修改如下：
    
        if(attributeExists("username"){
            setAttribute("username","joe");
        }
    
 9. 使用异常代替返回错误码
    

        if (deletePage(page) == E_OK)
    不会产生歧义，但是会导致更深层次的嵌套。
    
    try/catch 代码块抽离
    
        public void delete (Page page){
            try{
                deletePageAndxxx(page);
            }catch(Exception e){
                logError(e);
            }
        }
        
        private void deletePageAndxxx(Page page){
        }
        
        private void logError(Exception e){
        }
    错误处理就是一件事，不要包含其他逻辑。
    错误码通常是某个类或者枚举，会导致所有的类都要依赖它，每次需要重新编译。使用异常就无需重新编译和部署。
    
 10. Don't Repeat Yourself
 11. 结构化编程。
 

###第4章 注释
 1. 注释不能美化糟糕的代码
 2. 用代码来阐述
 3. 好的注释：

   	* 法律信息
   	* 提供信息的注释：比如解释某个抽象反复的返回值。
   	* 对意图的解释
   	* 阐释
   	* 警示：用于警告其他程序员会出现某种后果的注释
   	* TODO注释
   	* 放大：用来放大某种看来不合理之物的重要性。
   	* 公共API中的Javadoc
	
 4. 坏的注释：
	
     	* 自言自语：任何迫使读者查看其他模块的注释都不值得称赞。
     	* 多余的注释
     	* 误导性注释
     	* 循规式注释：每个函数都要有javadoc或者每个变量都要有注释的规矩是愚蠢可笑的。
     	* 日志式注释：每次编辑代码时，在模块开始处添加一条注释，会让模块变得凌乱不堪。
     	* 废话注释
     	* 可怕的废话
     		
     	/** The name. */
     	private String name;
     	
     	/** The version. */
     	private String version;
     	
     	* 能用函数或者变量时就别用注释
     	* 位置标记，如在源代码中标记：
     	// Actions ///////////////////////////
     	* 括号后面的注释
     	
     	public class WC {
     
     		private static Logger logger = LoggerFactory.getLogger(WC.class);
     	
     		public static void main(String[] args) {
     			BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
     			
     			String line ;
     			int lineCount = 0;
     			int charCount = 0;
     			int wordCount = 0;
     			try {
     				while ((line = in.readLine())!= null) {
     					lineCount++;
     					charCount += line.length();
     					String words[] = line.split("\\W");
     					wordCount += words.length;
     				}///while
     				
     				System.out.println(wordCount);
     				System.out.println(lineCount);
     				System.out.println(charCount);
     			} //try
     			catch (Exception e) {
     				// TODO: handle exception
     			}//catch
     		}//main
     	}
     	
     	* 归属与署名，这类信息最好用源代码控制系统
     	/** Add by zhu */
     	
     	* 注释掉的代码
     	* HTML注释
     	* 非本地信息：注释要和描述的代码离得最近。
     	* 信息过多
     	* 不明显的联系
     	* 函数头
     	* 范例
