<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <meta name="chart-name" content="Apple Watch 健身记录">
    <title>F2 图表组件库 - AntV</title>
    <link rel="stylesheet" href="https://gw.alipayobjects.com/os/rmsportal/YmDAMEQVbLJpVbKiRQVX.css" />

</head>
<body>
<script>/*Fixing iframe window.innerHeight 0 issue in Safari*/document.body.clientHeight;</script>

<script src="https://gw.alipayobjects.com/os/antv/assets/f2/3.2.2/f2.min.js"></script>

<script src="https://gw.alipayobjects.com/os/antv/assets/lib/jquery-3.2.1.min.js"></script>
<!-- 在 PC 上模拟 touch 事件 -->
<script src="https://gw.alipayobjects.com/os/rmsportal/NjNldKHIVQRozfbAOJUW.js"></script>

  <div class="chart-wrapper">
    <canvas id="mountNode"></canvas>
  </div>

Refering to
  [https://codepen.io/adeveloperdiary/pen/qOgpKK](https://codepen.io/adeveloperdiary/pen/qOgpKK)

<style>
  .chart-wrapper {
    background-color: #151515;
    box-shadow: 0px 0px 1px 0px #06060d;
  }
</style>
<script>
// customize shape and animation
var _F = F2,
  Shape = _F.Shape,
  Util = _F.Util,
  Global = _F.Global,
  G = _F.G,
  Animate = _F.Animate;
var Vector2 = G.Vector2;

Shape.registerShape('interval', 'tick', {
  draw: function draw(cfg, container) {
    var points = this.parsePoints(cfg.points);
    var style = Util.mix({
      stroke: cfg.color
    }, Global.shape.interval, cfg.style);
    if (cfg.isInCircle) {
      var newPoints = points.slice(0);
      if (this._coord.transposed) {
        newPoints = [points[0], points[3], points[2], points[1]];
      }

      var _cfg$center = cfg.center,
        x = _cfg$center.x,
        y = _cfg$center.y;

      var v = [1, 0];
      var v0 = [newPoints[0].x - x, newPoints[0].y - y];
      var v1 = [newPoints[1].x - x, newPoints[1].y - y];
      var v2 = [newPoints[2].x - x, newPoints[2].y - y];

      var startAngle = Vector2.angleTo(v, v1);
      var endAngle = Vector2.angleTo(v, v2);
      var r0 = Vector2.length(v0);
      var r = Vector2.length(v1);

      if (startAngle >= 1.5 * Math.PI) {
        startAngle = startAngle - 2 * Math.PI;
      }

      if (endAngle >= 1.5 * Math.PI) {
        endAngle = endAngle - 2 * Math.PI;
      }

      var lineWidth = r - r0;
      var newRadius = r - lineWidth / 2;

      return container.addShape('Arc', {
        className: 'interval',
        attrs: Util.mix({
          x: x,
          y: y,
          startAngle: startAngle,
          endAngle: endAngle,
          r: newRadius,
          lineWidth: lineWidth,
          lineCap: 'round',
          shadowColor: "rgba(0, 0, 0, 0.6)",
          shadowOffsetX: 0,
          shadowOffsetY: -5,
          shadowBlur: 50
        }, style)
      });
    }
  }
});

Animate.registerAnimation('waveIn', function(shape, animateCfg) {
  var startAngle = shape.attr('startAngle');
  var endAngle = shape.attr('endAngle');
  shape.attr('endAngle', startAngle);
  shape.animate().to(Util.mix({
    attrs: {
      endAngle: endAngle
    }
  }, animateCfg));
});
// ------

var data = [{
  name: 'activity1',
  percent: 2370,
  color: '#1ad5de',
  icon: 'stand.png',
  bgColor: '#183C3D'
}, {
  name: 'activity2',
  percent: 80,
  color: '#a0ff03',
  icon: 'walk.png',
  bgColor: '#324214'
}, {
  name: 'activity3',
  percent: 65,
  color: '#e90b3a',
  icon: 'run.png',
  bgColor: '#40131D'
}];

var chart = new F2.Chart({
  id: 'mountNode',
  width: 375,
  height: 260,
  pixelRatio: window.devicePixelRatio
});

chart.source(data, {
  percent: {
    max: 100
  }
});
chart.legend(false);
chart.coord('polar', {
  transposed: true,
  innerRadius: 0.382,
  radius: 0.8
});
chart.axis(false);
chart.interval().position('name*percent').color('color', function(val) {
  return val;
}).shape('tick').size(18).animate({
  appear: {
    animation: 'waveIn',
    duration: 1500,
    easing: 'elasticOut'
  },
  update: {
    duration: 1500,
    easing: 'elasticOut'
  }
});

data.map(function(obj) {
  // background
  chart.guide().arc({
    start: [obj.name, 0],
    end: [obj.name, 99.98],
    top: false,
    style: {
      lineWidth: 18,
      stroke: obj.bgColor
    }
  });
  chart.guide().html({
    position: [obj.name, 0],
    html: '<div style="width: 16px;height: 16px;">' + '![](http://www.adeveloperdiary.com/wp-content/uploads/2015/11/)' + '</div>'
  });
});
chart.render();

var updateData = function updateData() {
  for (var i = 0; i < data.length; ++i) {
    data[i].percent = Math.floor(Math.random() * 60 + 20);
  }
  chart.changeData(data);
  setTimeout(updateData, 1500);
};

setTimeout(updateData, 1500);
</script>

</body>
</html>
