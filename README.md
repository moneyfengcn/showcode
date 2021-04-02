# 求int的每个数字累加为个位值 ,不允许使用while、for、if 、else、switch、case等关键字,以及不允许使用递归方法
例如: int n=12345; 
  那么计算要求是为1+2+3+4+5累加起来，如果结果超过个位数还要重复,直至结果为一个位数
    1+2+3+4+5=15   15分解为1+5=6

最近无所事事，每天泡在各个群里找人吹牛。昨天有个群友出了这么一个题来考验我们，做为杠精界扛把子的我当然是第一时冲上去，在敲了条件边界后，我想出了这个方子，自己心里挺得意的，特此贴出来秀一秀。


# 代码例程：

```cs
  static class Program
    {


        volatile static private int _nCount = 0;
        /// <summary>
        /// 应用程序的主入口点。
        /// </summary>
        [STAThread]
        static void Main()
        {
            try
            {
                using (var timer = new System.Threading.Timer(new System.Threading.TimerCallback(On_TimerCallback), null, 2000, 2000))
                {
                    var sw = System.Diagnostics.Stopwatch.StartNew();
                    //正式测试代码 遍历int的全部值
                    for (int i = 0; i < int.MaxValue; i++)
                    {
                        _nCount = i;
                        //如果两种方法计算结果不一致说明有一方是错误的
                        if (A(_nCount) != B(_nCount))
                        {
                            Console.WriteLine("检验失败：{0}  ", _nCount.ToString());
                            break;
                        }
                    }

                    sw.Stop();
                    Console.WriteLine("检验完毕：耗时：" + sw.ElapsedMilliseconds);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("检验失败：{0}  {1}", _nCount.ToString(), ex.Message);
            }

            Console.ReadLine();
        }
        static void On_TimerCallback(object obj)
        {
            Console.WriteLine("计算进度 {0} %  循环：{1}", (int)((_nCount / int.MaxValue) * 100D), _nCount);
        }

        //标准答案 也是出题方希望得到的答案
        static int A(int n)
        {
            //很妙的式子，题主抛出来的时候兄弟们的眼睛都亮了           
            return (n - 1) % 9 + 1;
        }

        //这个方法有取巧的成份，try|goto的使用有争议
        //但严格意义来讲(抠字眼)，没有跑题        
        //代码上没有出现if\for\while\递归
        //主要以错误陷阱配合goto实现了if和while的功能
        //重点：(嗯嗯画重点了啊~！) 这方法是我想出来的
        static int B(int n)
        {
            int x = 0;
            string s = n.ToString();

            try
            {
            //这里实现了while 并且替代了递归
            Begin:
                try
                {
                    x = 0;
                  
                    int i = 0;
                Again:
                    x += int.Parse(s.Substring(i++, 1));  //通过i++越界引发异常
                    //这个goto可以展开，毕竟int long长度都是有限的
                    goto Again;
                }
                catch { }
                    
                s = x.ToString();
                
                //如果s.len==1(个位)就满足结果退出计算  (除0异常,跳过goto)
                var tmp = 1 / (s.Length - 1);
                //这个goto配合上面的除0和try 有点while的味道，但是严格上讲不是for|while               
                //goto是无条件转移，for while都是有条件转移，本质上的区别，虽然殊途同归               
                goto Begin;

            }
            catch { }

            return x;
        }
    }
```
