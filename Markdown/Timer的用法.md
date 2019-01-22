## Timer的用法

#### Timer根据一定的时间间隔执行N次事件

```C#
		//控制次数
        private static int i = 0;
        //实例化Timer类，设置间隔时间为10000毫秒； 
        private static Timer aTimer = new Timer(200);
        static void Main(string[] args)
        {
            //注册计时器的事件
            aTimer.Elapsed += new ElapsedEventHandler(CreateCode);
            //设置是执行一次（false）还是一直执行(true)，默认为true
            aTimer.AutoReset = true;
            //开始计时
            aTimer.Enabled = true;
            Console.ReadKey();
        }

private static void CreateCode(object sender, ElapsedEventArgs e)
        {
            string[] word = new string[] { "abcdefghijklmnopqrstuvwxyz", "ABCDEFGHIJKLMNOPQRSTUVWXYZ", "0123456789" };
            string code = string.Empty;
            Random random = new Random();
            while (code.Length != 7)
            {
                string typeStr = word[random.Next(0, word.Length)];
                code += typeStr[random.Next(0, typeStr.Length)];
            }
            Console.WriteLine($"第{i}次生成验证码：{code}\r\n");
            if (i == 100)
            {
                aTimer.Enabled = false;
            }
            i++;
        }
```

#### 执行的结果

```

第0次生成验证码：5kr4H8P

第1次生成验证码：K3C22U8

第2次生成验证码：7RO6L99

第3次生成验证码：iZy42MO

第4次生成验证码：xI3I191

第5次生成验证码：T86rjfi

第6次生成验证码：31Ang69

第7次生成验证码：85l3cEn

第8次生成验证码：4W82Y61

第9次生成验证码：0EG0UET

第10次生成验证码：5Nq8Rq3

第11次生成验证码：1v075DZ

第12次生成验证码：seL53pp

第13次生成验证码：O7SxpWs

第14次生成验证码：D0ctl33

第15次生成验证码：6J5pIVY

第16次生成验证码：2SXlEi5

第17次生成验证码：8Ai2AVE

第18次生成验证码：3j718hu

第19次生成验证码：9rD977K

第20次生成验证码：5an85ha

第21次生成验证码：JOuevND

第22次生成验证码：YW1zra7

第23次生成验证码：MFPvN5J

第24次生成验证码：0OarKzz

第25次生成验证码：6w4nG4P

第26次生成验证码：1fUj1Yf

第27次生成验证码：7nf2948

第28次生成验证码：38608Yl

第29次生成验证码：EK8kb1O

第30次生成验证码：TSHgxRe

第31次生成验证码：HbrcT1U

第32次生成验证码：Wk0xPQk

第33次生成验证码：4sMt4d0

第34次生成验证码：90xp3Qq

第35次生成验证码：532l1c2

第36次生成验证码：17Rha5V

第37次生成验证码：ZGZQg8Z

第38次生成验证码：OojMDIp

第39次生成验证码：Cx7iZv1

第40次生成验证码：RfEeVIu

第41次生成验证码：G5oz6u4

第42次生成验证码：789v52A

第43次生成验证码：32Jr3t6

第44次生成验证码：9Oung2G

第45次生成验证码：UcbWmn3

第46次生成验证码：Ik4SI0z

第47次生成验证码：XtWOFm6

第48次生成验证码：M0gKB9F

第49次生成验证码：A46g9L8

第50次生成验证码：P7Bc79L

第51次生成验证码：1Blx6Lb

第52次生成验证码：7K8tmxR

第53次生成验证码：py1CsE8

第54次生成验证码：D2NYO6K

第55次生成验证码：S5yUKD0

第56次生成验证码：H93Q2qQ

第57次生成验证码：VGTM1Dg

第58次生成验证码：KPdI9pW

第59次生成验证码：ZX5ev0m

第60次生成验证码：5GYzso0

第61次生成验证码：k7F3XV2

第62次生成验证码：y1qEUiV

第63次生成验证码：N40AQVl

第64次生成验证码：CTKW4hB

第65次生成验证码：QCvS37r

第66次生成验证码：FK2O2G2

第67次生成验证码：UtQKb7x

第68次生成验证码：IcaGxG4

第69次生成验证码：f6h5D5q

第70次生成验证码：t974ZzG

第71次生成验证码：iHC2W4w

第72次生成验证码：XPnC7Y4

第73次生成验证码：LY9Y54c

第74次生成验证码：AgHU4Y6

第75次生成验证码：PpsQhkH

第76次生成验证码：Dy1MdX9

第77次生成验证码：aM38J1b

第78次生成验证码：oUU6FQ6

第79次生成验证码：dDe50dg

第80次生成验证码：sL539Q8

第81次生成验证码：GuZ17cM

第82次生成验证码：VckAq51

第83次生成验证码：Kl7WnbS

第84次生成验证码：Y7FSj5i

第85次生成验证码：uIM0Pvl

第86次生成验证码：jQw8L30

第87次生成验证码：yz272uR

第88次生成验证码：mhR5122

第89次生成验证码：bqb40TX

第90次生成验证码：q942w2n

第91次生成验证码：E2W1sTD

第92次生成验证码：T6hYpft

第93次生成验证码：pEohUmW

第94次生成验证码：em9dR94

第95次生成验证码：tvJ95LC

第96次生成验证码：hdt839s

第97次生成验证码：w4162LI

第98次生成验证码：l8O5cxy

第99次生成验证码：z1y3yK5

第100次生成验证码：OL31uwe

```

