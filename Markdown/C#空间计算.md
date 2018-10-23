## C#空间计算

#### 判断一个坐标点p(lat,lng)到一个线段AB(A(lat1,lng1),B(lat2,lng2))的最短空间距离，以及计算两点之间的空间距离

```c#
		/// <summary>
        /// 经纬度坐标点到线段的最短距离（单位M）
        /// </summary>
        /// <param name="linestartlon">线段A端经度</param>
        /// <param name="linestartlat">线段A端纬度</param>
        /// <param name="lainendlon">线段B端经度</param>
        /// <param name="lainendlat">线段B端纬度</param>
        /// <param name="pointlon">点P端经度</param>
        /// <param name="pointlat">点P端纬度</param>
        /// <returns></returns>
        public static double pointToLineDistance(double linestartlon, double linestartlat, double lainendlon, double lainendlat, double pointlon, double pointlat)
        {
            //地球平均半径
            double earthRadius = 6371008.8;
            double y = Math.Sin(pointlon - linestartlon) * Math.Cos(pointlat);
            double x = Math.Cos(linestartlat) * Math.Sin(pointlat) - Math.Sin(linestartlat) * Math.Cos(pointlat) * Math.Cos(pointlat - linestartlat);
            double bearing1 = rad2deg(Math.Atan2(y, x));
            bearing1 = 360 - ((bearing1 + 360) % 360);

            double y2 = Math.Sin(lainendlon - linestartlon) * Math.Cos(lainendlat);
            double x2 = Math.Cos(linestartlat) * Math.Sin(lainendlat) - Math.Sin(linestartlat) * Math.Cos(lainendlat) * Math.Cos(lainendlat - linestartlat);
            double bearing2 = rad2deg(Math.Atan2(y2, x2));
            bearing2 = 360 - ((bearing2 + 360) % 360);

            double lat1Rads = deg2rad(linestartlat);
            double lat3Rads = deg2rad(pointlat);
            double dLon = deg2rad(pointlon - linestartlon);

            double distanceAC = Math.Acos(Math.Sin(lat1Rads) * Math.Sin(lat3Rads) + Math.Cos(lat1Rads) * Math.Cos(lat3Rads) * Math.Cos(dLon)) * earthRadius;
            double min_distance = Math.Abs(Math.Asin(Math.Sin(distanceAC / earthRadius) * Math.Sin(deg2rad(bearing1) - deg2rad(bearing2))) * earthRadius);
            return min_distance;

        }



        /// <summary>
        /// 根据两个经纬度坐标测距离
        /// </summary>
        /// <param name="startlon">A点经度</param>
        /// <param name="startlat">A点纬度</param>
        /// <param name="endlon">B点经度</param>
        /// <param name="endlat">B点纬度</param>
        /// <returns></returns>
        public static double pointToPointDistance(double startlon, double startlat, double endlon, double endlat)
        {
            //地球平均半径
            double R = 6371008.8;
            var rad1 = deg2rad(startlat);
            var rad2 = deg2rad(endlat);
            var latSubrad = deg2rad(endlat - startlat);
            var lonSubrad = deg2rad(endlon - startlon);
            var a = Math.Sin(latSubrad / 2) * Math.Sin(latSubrad / 2) + Math.Cos(rad1) * Math.Cos(rad2) * Math.Sin(lonSubrad / 2) * Math.Sin(lonSubrad / 2);
            var c = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            var d = R * c;
            return d;
        }


        /// <summary>
        /// 角度换为弧度 
        /// </summary>
        /// <param name="deg">角度</param>
        /// <returns></returns>
        public static double deg2rad(double deg)
        {
            return (deg * Math.PI / 180.0);
        }

        /// <summary>
        /// 弧度换为角度 
        /// </summary>
        /// <param name="rad">弧度</param>
        /// <returns></returns>
        public static double rad2deg(double rad)
        {
            return (rad / Math.PI * 180.0);
        }
```



#### 判断一个点是否属于一个多边形

```c#
		/// <summary>
        /// 确定给定点是否在多边形内
        /// </summary>
        /// <param name="polygon">多边形的顶点</param>
        /// <param name="aimPoint">目标点</param>
        /// <returns>true if the point is inside the polygon; otherwise, false</returns>
        public static bool IsPointInPolygon(PointF[] polygon, PointF aimPoint)
        {
            bool result = false;
            int j = polygon.Count() - 1;
            for (int i = 0; i < polygon.Count(); i++)
            {
                if (polygon[i].Y < aimPoint.Y && polygon[j].Y >= aimPoint.Y || polygon[j].Y < aimPoint.Y && polygon[i].Y >= aimPoint.Y)
                {
                    if (polygon[i].X + (aimPoint.Y - polygon[i].Y) / (polygon[j].Y - polygon[i].Y) * (polygon[j].X - polygon[i].X) < aimPoint.X)
                    {
                        result = !result;
                    }
                }
                j = i;
            }
            return result;
        }
```

#### 计算多边形质心

```c#
		/// <summary>
        /// 计算多边形质心的方法。这不适用于复杂多边形。
        /// </summary>
        /// <param name="poly">定义多边形的点</param>
        /// <returns>质心点，或PointF.Empty如果出错了</returns>
        public static PointF GetCentroid(PointF[] poly)
        {
            float accumulatedArea = 0.0f;
            float centerX = 0.0f;
            float centerY = 0.0f;

            for (int i = 0, j = poly.Count() - 1; i < poly.Count(); j = i++)
            {
                float temp = poly[i].X * poly[j].Y - poly[j].X * poly[i].Y;
                accumulatedArea += temp;
                centerX += (poly[i].X + poly[j].X) * temp;
                centerY += (poly[i].Y + poly[j].Y) * temp;
            }

            if (Math.Abs(accumulatedArea) < 1E-7f)
                return PointF.Empty;  // Avoid division by zero

            accumulatedArea *= 3f;
            return new PointF(centerX / accumulatedArea, centerY / accumulatedArea);
        }
```