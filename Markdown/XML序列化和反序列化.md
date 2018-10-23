## XML序列化和反序列化

#### XML文件内容

```xml
<?xml version='1.0' encoding='UTF-8'?>
<osm version='0.6' generator='JOSM'>
  <node id='-45118' action='modify' lat='30.57532058135' lon='114.26604011332' />
  <node id='-45119' action='modify' lat='30.5753500372' lon='114.26611164908' />
  <node id='-45121' action='modify' lat='30.57533748' lon='114.26608115296' />
  <node id='-45123' action='modify' lat='30.57541537533' lon='114.26604011363' />
  <node id='-45125' action='modify' lat='30.57536386994' lon='114.26606724936' />
  <node id='-45127' action='modify' lat='30.57534869803' lon='114.2660310939' />
  <node id='-45129' action='modify' lat='30.5754617013' lon='114.26615768109' />
  <node id='-45131' action='modify' lat='30.57551606065' lon='114.26612937781' />
  <node id='-45133' action='modify' lat='30.57550508167' lon='114.26610356274' />
  <node id='-45135' action='modify' lat='30.57552784296' lon='114.26615550392' />
  <node id='-45137' action='modify' lat='30.57552561543' lon='114.2661505646' />
  <node id='-45139' action='modify' lat='30.57550831771' lon='114.26611117171' />
  <way id='-45120' action='modify'>
    <nd ref='-45118' />
    <nd ref='-45121' />
    <nd ref='-45119' />
  </way>
  <way id='-45124' action='modify'>
    <nd ref='-45121' />
    <nd ref='-45125' />
    <nd ref='-45123' />
    <nd ref='-45129' />
    <nd ref='-45131' />
  </way>
  <way id='-45128' action='modify'>
    <nd ref='-45125' />
    <nd ref='-45127' />
  </way>
  <way id='-45134' action='modify'>
    <nd ref='-45133' />
    <nd ref='-45139' />
    <nd ref='-45131' />
    <nd ref='-45137' />
    <nd ref='-45135' />
  </way>
</osm>

```
#### XML在线生成的C#实体(XML属性标识不能缺少)

```c#
[XmlRoot(ElementName = "node")]
    public class Node
    {
        [XmlAttribute(AttributeName = "id")]
        public string Id { get; set; }
        [XmlAttribute(AttributeName = "action")]
        public string Action { get; set; }
        [XmlAttribute(AttributeName = "lat")]
        public string Lat { get; set; }
        [XmlAttribute(AttributeName = "lon")]
        public string Lon { get; set; }
    }

    [XmlRoot(ElementName = "nd")]
    public class Nd
    {
        [XmlAttribute(AttributeName = "ref")]
        public string Ref { get; set; }
    }

    [XmlRoot(ElementName = "way")]
    public class Way
    {
        [XmlElement(ElementName = "nd")]
        public List<Nd> Nd { get; set; }
        [XmlAttribute(AttributeName = "id")]
        public string Id { get; set; }
        [XmlAttribute(AttributeName = "action")]
        public string Action { get; set; }
    }

    [XmlRoot(ElementName = "osm")]
    public class Osm
    {
        [XmlElement(ElementName = "node")]
        public List<Node> Node { get; set; }
        [XmlElement(ElementName = "way")]
        public List<Way> Way { get; set; }
        [XmlAttribute(AttributeName = "version")]
        public string Version { get; set; }
        [XmlAttribute(AttributeName = "generator")]
        public string Generator { get; set; }

    }
```

#### XML序列化反序列化方法
```        c#
        public void objToXML()
        {
            string filePath = Path.Combine(AppDomain.CurrentDomain.GetData("DataDirectory").ToString(), "path.xml");
            List<Osm> myObject = new List<Osm>();
            //插入代码以设置对象的属性和字段。
            XmlSerializer mySerializer = new XmlSerializer(typeof(List<Osm>));
            //要写入文件，请创建StreamWriter对象。
            using (StreamWriter myWriter = new StreamWriter(filePath))
            {
                mySerializer.Serialize(myWriter, myObject);
            }
        }

        public void XMLtoObj()
        {
            string filePath = Path.Combine(AppDomain.CurrentDomain.GetData("DataDirectory").ToString(), "path.xml");
            Osm osm;
            //使用类型构造XmlSerializer的实例
            //正在反序列化的对象
            XmlSerializer mySerializer = new XmlSerializer(typeof(Osm));
            //要读取文件，请创建FileStream。
            using (FileStream myFileStream = new FileStream(filePath, FileMode.Open))
            {
                osm = (Osm)mySerializer.Deserialize(myFileStream);
            }
        }
```

       