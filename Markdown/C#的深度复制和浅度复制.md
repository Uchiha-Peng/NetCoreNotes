## C#的深度复制和浅度复制

List的深度复制和浅度复制

```List<int> lista = new List<int>() { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
            List<int> listb = new List<int>();
            List<int> listc = new List<int>(lista);
            List<int> listd = new List<int>();
            object obj = new object();
            listb = lista;
            listd.AddRange(lista);
            lista.RemoveAt(3);
```

