# 贝塞尔曲线的实现

## 前言
贝塞尔曲线（Bezier Curve）是一种与n个点相关的平滑曲线，其中贝塞尔曲线会经过第一个点和最后一个点。在Adobe Photoshop中，可以使用钢笔工具来创建贝塞尔曲线。上个动图来表现下：
![Bezier Curve On Photoshop](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTUxMDI3MTQzNzQ4NTI1?x-oss-process=image/format,png)

个人觉得这个玩意挺有意思，就自己在Android上实现了一下，我实现的效果如下图所示：

![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTUxMDI3MTQ0MDQ3MDUw?x-oss-process=image/format,png)

## 原理

原理什么的，我认为这张图足以诠释一切。图片来自wiki百科。
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTUxMDI3MTQ1MDQ4NzAy?x-oss-process=image/format,png)

找到动图中所显示的规律，你就知道贝塞尔曲线怎么回事了。

你会发现，我们给定了n个点，n个点之间有n-1条线段，我们在这n-1条线段上各取一个点，那么就有n-2个点，然后依次类推，直到只剩下一条线段，一个点，然后我们移动这些点，因此线段也会移动，线段上的点也跟着会变动，移动的规则是，每个点所在的位置在线段上所占的百分比都是相同的。

比如上图中，`P0` 和 `P1` 所构成的线段上的点，处于开始阶段，那么就是在 `P0` 的位置，和 `P0` 重合，其他的点也都处于开始位置，处于所在线段的开头。

然后逐渐以百分比的形式移动。

最终递归到最后一条线段，最后的那个所形成的轨迹，就是贝塞尔曲线了。不明白的，好好观察上面的动图吧。

## 实现
理解了原理后，我们就开始构思如何实现它，显然得到的是一堆点，并且，这些点是有顺序的，因为贝塞尔曲线会经过第一个点和最后一个点，而中间的点都只是影响到曲线的路径，并不一定会经过。

接下来要考虑到，生成曲线的过程是动态的，我们没法直观的得到，进度为t的时候，最终的那个点的位置在什么地方。t每一次改变，所有的点所有的线都会改变，因此我们需要动态的计算。也就是说，我们需要一个循环，从t=0，一直计算到t=100，每次递增1，这样我们就会计算100次，得到100个点。

再来考虑如何计算。假设有5个点，当前进度为t，那么我们可以计算得到4个点的坐标，同样的，通过4个点的坐标，和进度t，我们可以得到3个点的坐标，以此类推，值得得到一个点的坐标时，这个坐标就是贝塞尔曲线上的一个点。

因此，使用递归来做这个计算，在逻辑上是清晰的。

整理一下思路
1. 输入一个点的序列，点的数量是>=2的。
2. 当前进度为0，最大进度100，递增1。
3. 传入所有点的序列和当前进度，递归得到贝塞尔曲线的点。
4. 将点的坐标存起来，进度递增，执行3。
5. 进度结束，得到了100个在贝塞尔曲线上的点。

Bezier Curve的代码
```java
/**
 * How to use it
 *
 * BezierCurve bc = new BezierCurve();
 * bc.setPointSequence(PointFp[)
 *   .progressWith(1)   //optional
 *   .build();
 *
 * after above code run end, you will get a List of BezierCurve Point.
 *
 * Created by krosshuang on 2015/10/21.
 *
 */
public class BezierCurve {

    private static final String LOG_TAG = "BezierCurve";

    private List<PointF> mCurvePoints = null;
    private PointF[] mPointSequence = null;

    private static final int MAX_DIVIDED = 100;
    private float mProgressIncrement = 1;

    private float incrementY;
    private float incrementX;

    public BezierCurve setPointSequence(PointF[] pointSequence) {
        mPointSequence = pointSequence;
        return this;
    }

    /**
     * Progress increment that means how many times calculate for creating the Bezier Curve.
     * The value is 1.0 as default. So, progress is 0.0 on initial, and become 1.0, 2.0, 3.0, ..., 100.0, then over.
     * Every time, we will only create one Bezier Curve Point. so there are 100 points as default.
     * If you want to let your curve be more smooth, set progressIncrement to less value.
     * */
    public BezierCurve progressWith(float progressIncrement) {
        mProgressIncrement = progressIncrement;
        return this;
    }

    public List<PointF> build() {
        if (mPointSequence.length <= 1) {
            //only one point, no curve.
            Log.e(LOG_TAG, "only one point, could not create bezier curve. please check the parameter of function setPointSequence().");
            return null;
        }
        if (mCurvePoints == null) {
            mCurvePoints = new ArrayList<PointF>();
        } else {
            mCurvePoints.clear();
        }

        for (float i = 0; i <= MAX_DIVIDED; i += mProgressIncrement) {
            calculate(i, mPointSequence);
        }

        return mCurvePoints;
    }

    private void calculate(float progress, PointF[] pointList) {
        if (pointList.length == 2) {
            mCurvePoints.add(getPoint(progress, pointList[0], pointList[1]));
        } else if (pointList.length > 2) {
            PointF[] tempList = new PointF[pointList.length - 1];
            for (int i = 0; i < pointList.length - 1; i++) {
                tempList[i] = getPoint(progress, pointList[i], pointList[i + 1]);
            }
            calculate(progress, tempList);
        } else {
            //only one point, bad parameters.
            return;
        }
    }

    private PointF getPoint(float processPercent, PointF startPoint, PointF endPoint) {
        incrementY = (endPoint.y - startPoint.y) / MAX_DIVIDED;
        incrementX = (endPoint.x - startPoint.x) / MAX_DIVIDED;

        PointF p = new PointF();
        p.x = startPoint.x + processPercent * incrementX;
        p.y = startPoint.y + processPercent * incrementY;
        return p;
    }
}
```

## 四个点足矣

观察贝塞尔曲线在Photoshop上的表现，你会发现，PS只使用了4个点来形成一条贝塞尔曲线，理论上，贝塞尔曲线可以使用无数个点形成的。

上图使用了很多点来形成贝塞尔曲线，但是你看，上下交错的点对曲线的影响实际上抵消了，因此更多的控制点意义就不是很大了，而且还增加了计算量。

所以PS的钢笔工具之所以是这样的形式，也是经过了他们的工程师的思考所得到的最佳方案。

## Android上实现钢笔工具

计算贝塞尔曲线的点并不复杂，然而在Android上实现钢笔工具还是需要费点时间。

### 操作定义

我先对触屏的操作进行了定义。
1. double click 创建一个孤立的点，没有控制柄，贝塞尔曲线会经过。
2. double touch then drag 创建一个有控制柄的点，贝塞尔曲线会经过。

在android中的touch事件分别是：
1. down up down up
2. down up down move ...move up

在创建有控制柄的点时，拖拽过程会形成控制柄，控制柄的行为是：
1. 创建时，两个控制柄是中心对称的，在一条直线上，并且距离相等。
2. 创建后，两个控制柄在方向上是中心对称的，也就是在一条直线上，但是距离并不相等。

### 控制点的定义与操作
我们需要定义一下控制点的数据结构以及它所具有的操作。
```java
public class BezierControlPoint {

    public PointF prevPoint = new PointF();
    public PointF nextPoint = new PointF();
    public PointF vertex = new PointF();
    public boolean isSingle = false;
}
```

prevPoint和nextPoint表示控制柄上的两个点，曲线是不经过的，而vertex是曲线经过的点，三个点在一条直线上，prevPoint和nextPoint其中一个变化，另一个也会跟着变化。如果移动vertex，三个点同步移动。

接下来我们补充方法

```java
public class BezierControlPoint {

    public PointF prevPoint = new PointF();
    public PointF nextPoint = new PointF();
    public PointF vertex = new PointF();
    public boolean isSingle = false;

    public static BezierControlPoint createSingle(float x, float y) {

    }

    public static BezierControlPoint createMulti(float vertexX, float vertexY, float nextX, float nextY) {

    }

    public void moveVertex(float x, float y) {

    }

    public void moveNext(float x, float y, boolean isMirror) {

    }

    public void movePrev(float x, float y, boolean isMirror) {
    
    }
}

```
其中movePrev和moveNext的isMirror，表示是否镜像变化，如果isMirror -> true，那么除了角度的联动之外，距离也会保持相等，也就是关于vertex的中心对称。

### prevPoint 和 nextPoint 的联动变化

prevPoint和nextPoint会关于vertex进行联动变化，这个要怎么处理呢？假如我们在屏幕坐标系下处理的话，真的不好处理，因为旋转的角度不好处理，于是，我们需要把屏幕笛卡尔坐标系转换成极坐标系，然后再处理就简单多了。

转换后的坐标，有两个值，angle和r，angle是角度，r是相对于中心点的距离。

因此我们需要先把prevPoint和nextPoint计算出相当于vertex的坐标，也就是把vertex当成(0, 0)。
然后转换成极坐标系，touch的点计算出角度，用 `pi - angle` 就能得到另一个点的角度。
最后再转换成笛卡尔坐标上的点，再相对于vertex计算回去即可。

贴上所有代码
```java
public class BezierControlPoint {

    private static final String LOG_TAG = "BezierControlPoint";

    public static final int TYPE_PREV = 0;
    public static final int TYPE_VERTEX = 1;
    public static final int TYPE_NEXT = 2;

    public PointF prevPoint = new PointF();
    public PointF nextPoint = new PointF();
    public PointF vertex = new PointF();
    public boolean isSingle = false;


    public static BezierControlPoint createSingle(float x, float y) {
        BezierControlPoint p = new BezierControlPoint();
        p.isSingle = true;
        p.vertex.x = x;
        p.vertex.y = y;
        return p;
    }

    /**
     * create multi control point
     * */
    public static BezierControlPoint createMulti(float vertexX, float vertexY, float nextX, float nextY) {
        BezierControlPoint p = new BezierControlPoint();
        p.isSingle = false;
        p.vertex.x = vertexX;
        p.vertex.y = vertexY;
        p.nextPoint.x = nextX;
        p.nextPoint.y = nextY;
        p.prevPoint.x = 2 * p.vertex.x - p.nextPoint.x;
        p.prevPoint.y = 2 * p.vertex.y - p.nextPoint.y;
        return p;
    }

    /**
     * move vertex
     * */
    public void moveVertex(float x, float y) {
        Log.i(LOG_TAG, "moveVertex");
        float deltaX = x - vertex.x;
        float deltaY = y - vertex.y;
        vertex.x = x;
        vertex.y = y;
        prevPoint.x += deltaX;
        prevPoint.y += deltaY;
        nextPoint.x += deltaX;
        nextPoint.y += deltaY;
    }

    public void moveNext(float x, float y, boolean isMirror) {
        Log.i(LOG_TAG, "moveNext x: " + x + " y: " + y + " isMirror: " + isMirror);
        if (isMirror) {
            float deltaX = x - vertex.x;
            float deltaY = y - vertex.y;
            prevPoint.x = vertex.x - deltaX;
            prevPoint.y = vertex.y - deltaY;
            nextPoint.x = x;
            nextPoint.y = y;
            return;
        } else {
            float nextEndX = x - vertex.x;
            float nextEndY = y - vertex.y;
            float relativePrevX = prevPoint.x - vertex.x;
            float relativePrevY = prevPoint.y - vertex.y;

            PolarPointF polarPrev = PolarPointF.parseFromCartesianCoordinatePoint(relativePrevX, relativePrevY);
            PolarPointF polarNextEnd = PolarPointF.parseFromCartesianCoordinatePoint(nextEndX, nextEndY);

            polarPrev.angle =  - (float)Math.PI + polarNextEnd.angle;

            prevPoint = polarPrev.toCartesianCoordinatePoint();
            prevPoint.x = prevPoint.x + vertex.x;
            prevPoint.y = prevPoint.y + vertex.y;
            nextPoint.x = x;
            nextPoint.y = y;
        }
        //Log.i(LOG_TAG, "prev: " + prevPoint.toString() + " vertex: " + vertex.toString() + " next: " + nextPoint.toString());
    }

    public void movePrev(float x, float y, boolean isMirror) {
        Log.i(LOG_TAG, "movePrev");
        if (isMirror) {
            float deltaX = x - vertex.x;
            float deltaY = y - vertex.y;
            prevPoint.x = vertex.x - deltaX;
            prevPoint.y = vertex.y - deltaY;
            nextPoint.x = x;
            nextPoint.y = y;
            return;
        } else {
            float prevEndX = x - vertex.x;
            float prevEndY = y - vertex.y;
            float relativeNextX = nextPoint.x - vertex.x;
            float relativeNextY = nextPoint.y - vertex.y;

            PolarPointF polarNext = PolarPointF.parseFromCartesianCoordinatePoint(relativeNextX, relativeNextY);
            PolarPointF polarPrevEnd = PolarPointF.parseFromCartesianCoordinatePoint(prevEndX, prevEndY);

            polarNext.angle =  - (float)Math.PI + polarPrevEnd.angle;

            nextPoint = polarNext.toCartesianCoordinatePoint();
            nextPoint.x = nextPoint.x + vertex.x;
            nextPoint.y = nextPoint.y + vertex.y;
            prevPoint.x = x;
            prevPoint.y = y;
        }
    }
}
```

以上就实现了在android上的钢笔工具的功能。