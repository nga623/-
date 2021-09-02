using System;
 
using System.Collections.Generic;
using System.Linq;
 
namespace MyProject
{
   
    class Program
    {
        /// <summary>
        /// unicode转字符
        /// </summary>
        /// <param name="str">unicode</param>
        /// <returns>字符</returns>
        public static string UnicodeToString(string str)
        {

            string outStr = "";
            if (!string.IsNullOrEmpty(str))
            {
                string[] strlist = str.Replace("\\", "").Split('u');
                try
                {
                    for (int i = 1; i < strlist.Length; i++)
                    {
                       
                        outStr += (char)int.Parse(strlist[i], System.Globalization.NumberStyles.HexNumber);
                    }
                }
                catch (FormatException ex)
                {
                    outStr = ex.Message;
                }
            }
            return outStr;
        }

        /// <summary>
        /// 获取字典key最大值
        /// </summary>
        /// <param name="listKey">字典keys</param>
        /// <returns>maxKey</returns>
        private static int KeyMaxSize(List<string> listKey)
        {
            int max = 0;

            List<int> listSize = new List<int>();
            for (int i = 0; i < listKey.Count; i++)
            {
                listSize.Add(listKey[i].Length);
            }
            max = listSize.Max();
            return max;
        }

        /// <summary>
        /// 获取value最大值
        /// </summary>
        /// <param name="listValue">字典Values</param>
        /// <returns>maxValue</returns>
        private static int ValueMaxSize(List<int> listValue)
        {
            int max = 0;
            int maxValue = listValue.Max();
            List<int> listSize = new List<int>();
            for (int i = 0; i < listValue.Count; i++)
            {
                listSize.Add(listValue[i] / maxValue * 20);
            }
            max = listSize.Max();
            return max;
        }

        /// <summary>
        /// 显示图标
        /// </summary>
        /// <param name="str">输入字符</param>
        private static void ShowChart(string str)
        {
            
            #region 提取字符串，并将数据插入字典
            string[] temp = str.Split("\r\n");//按照\r\n split
            int num = Convert.ToInt32(temp[0]);
            string sortStyle = temp[1].Split(" ")[0];//Name或者Value 排序
            string upOrDown = temp[1].Split(" ")[1];//表示升序或是降序
            Dictionary<string, int> map = new Dictionary<string, int>(); //创建字典存储数据string,int
            //第三行开始数据插入字典 key是字符串S， value是V对应的整数
            for (int i = 2; i < num + 2; i++)
            {
                map.Add(temp[i].Split(" ")[0], int.Parse(temp[i].Split(" ")[1]));
            }
            #endregion

            #region 参数校验
            if (num < 1 || num > 20)
            { 
                Console.WriteLine("行数不在范围内，1<=num<=20");
                return;
            }
            if ("Name".Equals(sortStyle) == false && "Value".Equals(sortStyle) == false)
            { 
                Console.WriteLine("请输入正确的排序方式，Name or Value");
                return;
            }
            if ("ASC".Equals(upOrDown) == false && "DESC".Equals(upOrDown) == false)
            { 
                Console.WriteLine("请输入正确的升降序方式，ASC or DESC");
                return;
            }
            #endregion

            #region 字典的数据进行要求排序
            Dictionary<string, int> sorpDic = new Dictionary<string, int>();
            if (sortStyle.Equals("Value"))
            {
                if (upOrDown.Equals("ASC")) //value 升序
                {
                    sorpDic = map.OrderBy(v => v.Value).ToDictionary(o => o.Key, p => p.Value);
                }
                else // value 降序
                {
                    sorpDic = map.OrderByDescending(v => v.Value).ToDictionary(o => o.Key, p => p.Value);
                }
            }
            else
            {
                if (upOrDown.Equals("ASC")) //key 升序
                {
                    sorpDic = map.OrderBy(v => v.Key).ToDictionary(o => o.Key, p => p.Value);
                }
                else // key 降序
                {
                    sorpDic = map.OrderByDescending(v => v.Key).ToDictionary(o => o.Key, p => p.Value);
                }

            }
            #endregion

            #region  显示图表
            var keyList = sorpDic.Select(p => p.Key).ToList();
            var valueList = sorpDic.Select(p => p.Value).ToList();
            var keyMaxSize = KeyMaxSize(keyList);
            var valueMaxSize = ValueMaxSize(valueList);
            for (int i = 1, m = 0; i < 2 * num + 2; i++)
            {
                if (i == 1)
                { //首行
                    Console.Write(UnicodeToString("\\u250c")); //┌
                    int j = keyMaxSize;
                    while (j > 0)
                    {
                        Console.Write(UnicodeToString("\\u2500"));//─
                        j--;
                    }
                    Console.Write(UnicodeToString("\\u252c"));//┬
                    int k = valueList.Max();
                    while (k > 0)
                    {
                        Console.Write(UnicodeToString("\\u2500"));//─
                        k--;
                    }
                    Console.WriteLine(UnicodeToString("\\u2510"));//┐
                }
                else if (i % 2 == 0)
                { //数据行
                    Console.Write(UnicodeToString("\\u2502"));//│
                    int j = keyMaxSize - keyList[m].Length;
                    while (j > 0)
                    {
                        Console.Write(" ");
                        j--;
                    }
                    Console.Write(keyList[m]);
                    Console.Write(UnicodeToString("\\u2502"));
                    int k = sorpDic[(keyList[m])];
                    int k2 = valueList.Max() - k;
                    while (k > 0)
                    {
                        Console.Write(UnicodeToString("\\u2588"));
                        k--;
                    }
                    while (k2 > 0)
                    {
                        Console.Write(" ");
                        k2--;
                    }
                    Console.WriteLine(UnicodeToString("\\u2502"));
                    m++;
                }
                else if (i == 2 * num + 1)
                {//最后一行
                    Console.Write("\u2514");//└
                    int j = keyMaxSize;
                    while (j > 0)
                    {
                        Console.Write(UnicodeToString("\\u2500"));
                        j--;
                    }
                    Console.Write(UnicodeToString("\\u2534"));
                    int k = valueList.Max();
                    while (k > 0)
                    {
                        Console.Write(UnicodeToString("\\u2500"));
                        k--;
                    }
                    Console.WriteLine(UnicodeToString("\\u2518"));//┘
                }
                else
                {//其他行（无数据，也不是在首行或者末行）
                    Console.Write(UnicodeToString("\\u251c"));//├
                    int j = keyMaxSize;
                    while (j > 0)
                    {
                        Console.Write(UnicodeToString("\\u2500"));
                        j--;
                    }
                    Console.Write(UnicodeToString("\\u253c"));
                    int k = valueList.Max();
                    while (k > 0)
                    {
                        Console.Write(UnicodeToString("\\u2500"));
                        k--;
                    }
                    Console.WriteLine(UnicodeToString("\\u2524"));
                }
            }
            #endregion
        }

        static void Main(string[] args)
        {
            ShowChart("3\r\nValue DESC\r\napple 5\r\npen 3\r\npineapple 10");
             
        }
       

    }

}
