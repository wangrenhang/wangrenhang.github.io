---
layout: false
---
{% raw %}
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Constellation</title>

    <script
              src="https://code.jquery.com/jquery-2.2.4.min.js"
              integrity="sha256-BbhdlvQf/xTY9gja0Dq3HiwQF8LaCRTXxZKRutelT44="
              crossorigin="anonymous">
              </script>

    <style type="text/css">
      #starmap {
         z-index: 8;
         text-align: center;
      }
    </style>
    
  </head>
  <style>body{background-color:black;}</style>
  <body style="margin: 0 auto">
    <canvas style="position:absolute;z-index:-1"></canvas>
<script type="text/javascript">

      if (!window.requestAnimationFrame) {
        window.requestAnimationFrame = (window.webkitRequestAnimationFrame || window.mozRequestAnimationFrame || window.msRequestAnimationFrame || window.oRequestAnimationFrame || function (callback) {
          return window.setTimeout(callback, 1000 / 60);
        });
      }

      /*
       * @author Acauã Montiel <contato@acauamontiel.com.br>
       * @license http://acaua.mit-license.org/
       */
      (function ($, window) {
        function Constellation (canvas, options) {
          var $canvas = $(canvas),
            context = canvas.getContext('2d'),
            defaults = {
              star: {
                color: 'rgba(255, 255, 255, .5)',
                width: 1,
                randomWidth: true
              },
              line: {
                color: 'rgba(255, 255, 255, .5)',
                width: 0.2
              },
              position: {
                x: 0, // This value will be overwritten at startup
                y: 0 // This value will be overwritten at startup
              },
              width: window.innerWidth,
              height: window.innerHeight,
              velocity: 0.3,
              length: 100,
              distance: 120,
              radius: 150,
              stars: []
            },
            config = $.extend(true, {}, defaults, options);

          function Star () {
            this.x = Math.random() * canvas.width;
            this.y = Math.random() * canvas.height;

            this.vx = (config.velocity - (Math.random() * 0.5));
            this.vy = (config.velocity - (Math.random() * 0.5));

            this.radius = config.star.randomWidth ? (Math.random() * config.star.width) : config.star.width;
          }

          Star.prototype = {
            create: function(){
              context.beginPath();
              context.arc(this.x, this.y, this.radius, 0, Math.PI * 2, false);
              context.fill();
            },

            animate: function(){
              var i;
              for (i = 0; i < config.length; i++) {

                var star = config.stars[i];

                if (star.y < 0 || star.y > canvas.height) {
                  star.vx = star.vx;
                  star.vy = - star.vy;
                } else if (star.x < 0 || star.x > canvas.width) {
                  star.vx = - star.vx;
                  star.vy = star.vy;
                }

                star.x += star.vx;
                star.y += star.vy;
              }
            },

            line: function(){
              var length = config.length,
                iStar,
                jStar,
                i,
                j;

              for (i = 0; i < length; i++) {
                for (j = 0; j < length; j++) {
                  iStar = config.stars[i];
                  jStar = config.stars[j];

                  if (
                    (iStar.x - jStar.x) < config.distance &&
                    (iStar.y - jStar.y) < config.distance &&
                    (iStar.x - jStar.x) > - config.distance &&
                    (iStar.y - jStar.y) > - config.distance
                  ) {
                    if (
                      (iStar.x - config.position.x) < config.radius &&
                      (iStar.y - config.position.y) < config.radius &&
                      (iStar.x - config.position.x) > - config.radius &&
                      (iStar.y - config.position.y) > - config.radius
                    ) {
                      context.beginPath();
                      context.moveTo(iStar.x, iStar.y);
                      context.lineTo(jStar.x, jStar.y);
                      context.stroke();
                      context.closePath();
                    }
                  }
                }
              }
            }
          };

          this.createStars = function () {
            var length = config.length,
              star,
              i;

            context.clearRect(0, 0, canvas.width, canvas.height);

            for (i = 0; i < length; i++) {
              config.stars.push(new Star());
              star = config.stars[i];

              star.create();
            }

            star.line();
            star.animate();
          };

          this.setCanvas = function () {
            canvas.width = config.width;
            canvas.height = config.height;
          };

          this.setContext = function () {
            context.fillStyle = config.star.color;
            context.strokeStyle = config.line.color;
            context.lineWidth = config.line.width;
          };

          this.setInitialPosition = function () {
            if (!options || !options.hasOwnProperty('position')) {
              config.position = {
                x: canvas.width * 0.5,
                y: canvas.height * 0.5
              };
            }
          };

          this.loop = function (callback) {
            callback();

            window.requestAnimationFrame(function () {
              this.loop(callback);
            }.bind(this));
          };

          this.bind = function () {
            $canvas.on('mousemove', function(e){
              config.position.x = e.pageX - $canvas.offset().left;
              config.position.y = e.pageY - $canvas.offset().top;
            });
          };

          this.init = function () {
            this.setCanvas();
            this.setContext();
            this.setInitialPosition();
            this.loop(this.createStars);
            this.bind();
          };
        }

        $.fn.constellation = function (options) {
          return this.each(function () {
            var c = new Constellation(this, options);
            c.init();
          });
        };
      })($, window);

      // Init plugin
      $('canvas').constellation({
        star: {
          width: 3
        },
        line: {
          color: 'rgba(10, 120, 240, .5)',
          width: 0.4
        },
        radius: 120
      });
    </script>
      <div id="starmap">
          <p align="center">
            <iframe width="900" height="600" frameborder="0" scrolling="no" marginheight="0" marginwidth="0" src="https://virtualsky.lco.global/embed/index.html?longitude=0&latitude=89.99991&projection=gnomic&keyboard=false&constellations=true&constellationlabels=true&showstarlabels=true&scalestars=2&magnitude=100&live=true" allowTransparency="true"></iframe>
          </p>
    </div>
  </body>
</html>
{% endraw %}
