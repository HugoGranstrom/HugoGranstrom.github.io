---
layout: page
title: Gravity Simulator
categories: portfolio
shortdescription: A gravity simulator of the solar system written in python (VPython)
---

This is a gravity simulator I have written in python using the library VPython/Glowscript. This project was inspired by an exercise my math teacher gave us where we were supposed to simulate the moons orbit around the earth in excel using differential equations. Luckily there exists better tools for this, like python in conjuction with VPython where you get both vectors and graphics. So no need for splitting the vectors in an X-part and an Y-part, that is really convinient actually. My first intention was to just write the earth-moon simulation but then I thought: "Why not do the entire solar system instead". And so I did, the Sun, the eight planets and Pluto (you will forever be a planet to me) all dancing together in a cosmic dance. Futhermore, I choosed to used the Euler method for integration because it is easy to implent and it is the one most people are familiar with. This simulation won't be near accurate enough to simulate the actual solar system anyway (for example realtivity is not accounted for and the timestep is too big) so the inaccuracy doesn't bother me. I just want to see some planets dance... and throw in a black hole later. I have also choosen to use other units then the SI-units: distance is measured in AU (average distance between earth and the sun), time is measured in days and mass in solar masses. This is because then the computer doesn't have to handle such large numbers and hence it consumes less memory. A consequence of this is that G, the gravitational constant changes it's values from the familiar 6.67e-11 to 2.959e-04. Watch it and have fun! 

The source code can be found on glowscript [here](http://www.glowscript.org/#/user/hajenzoo/folder/Public/program/gravity-simulator) (click "View this program" to see the source code)

The simulation with a black hole can be found [here](http://www.glowscript.org/#/user/hajenzoo/folder/Public/program/gravity-simulator-black-hole) (click "Run this program" in the upper left corner to start it)

A tutorial on how to code this yourself will be coming in the future. I the meanwhile if you're interested, I have written comments in the code that may be helpful. 

Remember, you can always count on your best friend, the calculator!

Controls:
- Zoom: scroll
- Pan: Hold SHIFT and drag with left mouse button
- Rotate: Hold and drag right mouse button

<div id="glowscript" class="glowscript">
<script type="text/javascript" src="https://s3.amazonaws.com/glowscript/lib/jquery/2.1/jquery.min.js"></script>
<script type="text/javascript" src="https://s3.amazonaws.com/glowscript/lib/jquery/2.1/jquery-ui.custom.min.js"></script>
<script type="text/javascript" src="https://s3.amazonaws.com/glowscript/package/glow.2.7.min.js"></script>
<script type="text/javascript" src="https://s3.amazonaws.com/glowscript/package/RSrun.2.7.min.js"></script>
<script type="text/javascript"><!--//--><![CDATA[//><!--
;(function() { var __rt=srequire('streamline/lib/callbacks/runtime').runtime(__filename, false),__func=__rt.__func,__cb=__rt.__cb; var RS_modules = {};
RS_modules.pythonize = {};

(function() {
  function strings() {
    var string_funcs, exclude, name;
    string_funcs = set("capitalize strip lstrip rstrip islower isupper isspace lower upper swapcase center count endswith startswith find rfind index rindex format join ljust rjust partition rpartition replace split rsplit splitlines zfill".split(" "));
    if (!arguments.length) {
      exclude = (function() {
        var s = RS_set();
        s.jsset.add("split");
        s.jsset.add("replace");
        return s;
      })(); }
     else if (arguments[0]) {
      exclude = Array.prototype.slice.call(arguments); }
     else {
      exclude = null; }  ;

    if (exclude) {
      string_funcs = string_funcs.difference(set(exclude)); } ;

    var RS_Iter0 = RS_Iterable(string_funcs);
    for (var RS_Index0 = 0; RS_Index0["<"](RS_Iter0.length); RS_Index0++) {
      name = RS_Iter0[RS_Index0];
      (RS_expr_temp = String.prototype)[((((typeof name === "number") && name["<"](0))) ? RS_expr_temp.length["+"](name) : name)] = (RS_expr_temp = RS_str.prototype)[((((typeof name === "number") && name["<"](0))) ? RS_expr_temp.length["+"](name) : name)]; }; };  RS_modules.pythonize.strings = strings;
})();
function main(_) { var version, box, sphere, cylinder, pyramid, cone, helix, ellipsoid, ring, arrow, compound, display, vector, print, scene, RS_ls, G, dt, time, scale_factor, AU, M, bodies, sun, earth, mercury, venus, mars, jupiter, saturn, uranus, neptune, pluto, body, __name__, strings, RS_Iter4, RS_Index4;


  function Body() {
    if ((this.RS_object_id === undefined)) { Object.defineProperty(this, "RS_object_id", { value: ++RS_object_counter }); };
    Body.prototype.__init__.apply(this, arguments); }; var __frame = { name: "main", line: 32 }; return __func(_, this, arguments, main, 0, __frame, function __$main() { version = RS_list_decorate(["2.7","glowscript",]); Array.prototype["+"] = function(r) { return this.concat(r); }; Array.prototype["*"] = function(r) { return __array_times_number(this, r); }; __name__ = "__main__"; window.__GSlang = "vpython"; box = vp_box; sphere = vp_sphere; cylinder = vp_cylinder; pyramid = vp_pyramid; cone = vp_cone; helix = vp_helix; ellipsoid = vp_ellipsoid; ring = vp_ring; arrow = vp_arrow; compound = vp_compound; display = canvas; vector = vec; print = GSprint; scene = canvas(); strings = RS_modules.pythonize.strings; strings(); "8"; G = 0.00029592; "9"; dt = 0.01; "10"; time = 0; "11"; scale_factor = 1000; "12"; AU = 150000000000; "13"; M = 2e+30; "15"; bodies = RS_list_decorate([]); "17";

    Body.prototype.__init__ = function __init__() {
      var self = this;
      var mass = ((((arguments[0] === undefined) || (((((0 === arguments.length["-"](1)) && (arguments[arguments.length["-"](1)] !== null)) && (typeof arguments[arguments.length["-"](1)] === "object")) && (arguments[arguments.length["-"](1)][RS_kwargs_symbol] === true))))) ? __init__.__defaults__.mass : arguments[0]);
      var radius = ((((arguments[1] === undefined) || (((((1 === arguments.length["-"](1)) && (arguments[arguments.length["-"](1)] !== null)) && (typeof arguments[arguments.length["-"](1)] === "object")) && (arguments[arguments.length["-"](1)][RS_kwargs_symbol] === true))))) ? __init__.__defaults__.radius : arguments[1]);
      var velocity = ((((arguments[2] === undefined) || (((((2 === arguments.length["-"](1)) && (arguments[arguments.length["-"](1)] !== null)) && (typeof arguments[arguments.length["-"](1)] === "object")) && (arguments[arguments.length["-"](1)][RS_kwargs_symbol] === true))))) ? __init__.__defaults__.velocity : arguments[2]);
      var position = ((((arguments[3] === undefined) || (((((3 === arguments.length["-"](1)) && (arguments[arguments.length["-"](1)] !== null)) && (typeof arguments[arguments.length["-"](1)] === "object")) && (arguments[arguments.length["-"](1)][RS_kwargs_symbol] === true))))) ? __init__.__defaults__.position : arguments[3]);
      var color = ((((arguments[4] === undefined) || (((((4 === arguments.length["-"](1)) && (arguments[arguments.length["-"](1)] !== null)) && (typeof arguments[arguments.length["-"](1)] === "object")) && (arguments[arguments.length["-"](1)][RS_kwargs_symbol] === true))))) ? __init__.__defaults__.color : arguments[4]);
      var trail = ((((arguments[5] === undefined) || (((((5 === arguments.length["-"](1)) && (arguments[arguments.length["-"](1)] !== null)) && (typeof arguments[arguments.length["-"](1)] === "object")) && (arguments[arguments.length["-"](1)][RS_kwargs_symbol] === true))))) ? __init__.__defaults__.trail : arguments[5]);
      var name = ((((arguments[6] === undefined) || (((((6 === arguments.length["-"](1)) && (arguments[arguments.length["-"](1)] !== null)) && (typeof arguments[arguments.length["-"](1)] === "object")) && (arguments[arguments.length["-"](1)][RS_kwargs_symbol] === true))))) ? __init__.__defaults__.name : arguments[6]);
      var RS_kwargs_obj = arguments[arguments.length["-"](1)];
      if ((((RS_kwargs_obj === null) || (typeof RS_kwargs_obj !== "object")) || (RS_kwargs_obj[RS_kwargs_symbol] !== true))) { RS_kwargs_obj = { }; };
      if (Object.prototype.hasOwnProperty.call(RS_kwargs_obj, "mass")) {
        mass = RS_kwargs_obj.mass; } ;

      if (Object.prototype.hasOwnProperty.call(RS_kwargs_obj, "radius")) {
        radius = RS_kwargs_obj.radius; } ;

      if (Object.prototype.hasOwnProperty.call(RS_kwargs_obj, "velocity")) {
        velocity = RS_kwargs_obj.velocity; } ;

      if (Object.prototype.hasOwnProperty.call(RS_kwargs_obj, "position")) {
        position = RS_kwargs_obj.position; } ;

      if (Object.prototype.hasOwnProperty.call(RS_kwargs_obj, "color")) {
        color = RS_kwargs_obj.color; } ;

      if (Object.prototype.hasOwnProperty.call(RS_kwargs_obj, "trail")) {
        trail = RS_kwargs_obj.trail; } ;

      if (Object.prototype.hasOwnProperty.call(RS_kwargs_obj, "name")) {
        name = RS_kwargs_obj.name; } ;

      var RS_ls;
      "19";
      self.mass = mass;
      "20";
      self.velocity = velocity;
      "21";
      self.position = position;
      "22";
      self.color = color;
      "23";
      self.radius = radius;
      "24";
      self.forces = RS_list_decorate([]);
      "25";
      self.acc = vector(0, 0, 0);
      "26";
      self.sum_force = vector(0, 0, 0);
      "27";
      self.name = name;
      "28";
      self.label = RS_interpolate_kwargs.call(this, label, [RS_desugar_kwargs({ pos: self.position, text: self.name, height: 10 }),]);
      "29";
      self.sphere = RS_interpolate_kwargs.call(this, sphere, [RS_desugar_kwargs({ pos: self.position, color: self.color, radius: self.radius["*"](scale_factor), make_trail: trail, retain: 200 }),]);
      "30";
      bodies.append(self); };

    if (!Body.prototype.__init__.__defaults__) { Object.defineProperties(Body.prototype.__init__, {
        __defaults__: { value: { mass: 1, radius: 1, velocity: vector(0, 0, 0), position: vector(0, 0, 0), color: color.white, trail: true, name: "Body" } },
        __handles_kwarg_interpolation__: { value: true },
        __argnames__: { value: ["mass","radius","velocity","position","color","trail","name",] } }); } ;

    Body.__argnames__ = Body.prototype.__init__.__argnames__;
    Body.__handles_kwarg_interpolation__ = Body.prototype.__init__.__handles_kwarg_interpolation__;
    Body.prototype.updateOld = function updateOld() {
      var self = this;
      var RS_ls, force;
      "32";
      self.forces = RS_list_decorate([]);
      "33";
      self.sum_force = vector(0, 0, 0);
      "34";
      self.gravitational_force();
      "38";
      var RS_Iter1 = RS_Iterable(self.forces);
      for (var RS_Index1 = 0; RS_Index1["<"](RS_Iter1.length); RS_Index1++) {
        force = RS_Iter1[RS_Index1];
        "39";
        self.sum_force = self.sum_force["+="](force); };

      "41";
      self.acc = self.sum_force["/"](self.mass);
      "42";
      self.velocity = self.velocity["+="](dt["*"](self.acc));
      "43";
      self.position = self.position["+="](dt["*"](self.velocity));
      "45";
      self.sphere.pos = self.position; };

    Body.prototype.gravitational_force = function gravitational_force() {
      var self = this;
      var RS_ls, r, force, dir, body;
      "49";
      var RS_Iter2 = RS_Iterable(bodies);
      for (var RS_Index2 = 0; RS_Index2["<"](RS_Iter2.length); RS_Index2++) {
        body = RS_Iter2[RS_Index2];
        "50";
        r = mag(self.position["-"](body.position));
        "51";
        if (r["<"](self.radius["+"](body.radius))) {
          "52";
          continue; } ;

        "53";
        force = G["*"](self.mass)["*"](body.mass)["/"](GS_power(r, 2));
        "54";
        dir = norm(body.position["-"](self.position));
        "55";
        force = force["*"](dir);
        "56";
        self.forces.append(force); }; };


    Body.prototype.update = function update() {
      var self = this;
      var RS_ls, force;
      "58";
      self.forces = RS_list_decorate([]);
      "59";
      self.sum_force = vector(0, 0, 0);
      "60";
      self.gravitational_force();
      "64";
      var RS_Iter3 = RS_Iterable(self.forces);
      for (var RS_Index3 = 0; RS_Index3["<"](RS_Iter3.length); RS_Index3++) {
        force = RS_Iter3[RS_Index3];
        "65";
        self.sum_force = self.sum_force["+="](force); };

      "68";
      self.position = self.position["+="](self.velocity["*"](dt)["+"](self.acc["/"](2)["*"](GS_power(dt, 2))));
      "69";
      self.velocity = self.velocity["+="](dt["/"](2)["*"](self.acc["+"](self.sum_force["/"](self.mass))));
      "70";
      self.acc = self.sum_force["/"](self.mass);
      "72";
      self.sphere.pos = self.position;
      "73";
      self.label.pos = self.position; };

    Body.prototype.__repr__ = function __repr__() {
      return "<"["+"](__name__)["+"](".")["+"](this.constructor.name)["+"](" #")["+"](this.RS_object_id)["+"](">"); };

    Body.prototype.__str__ = function __str__() {
      return this.__repr_; };

    Object.defineProperty(Body.prototype, "__bases__", { value: [] });
    Body.prototype.RS_ls = "18";
    Body.prototype.RS_ls = "31";
    Body.prototype.RS_ls = "48";
    Body.prototype.RS_ls = "57";

    "78";
    sun = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: 1, radius: 700000000["*"](5)["/"](scale_factor)["/"](AU), color: color.yellow, trail: false, name: "Sun" }),]);
    "86";
    earth = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: 6e+24["/"](M), radius: 6371000["/"](AU), position: vector(0.5111702950987252["-u"](), 0.8734341386147972["-u"](), 0.00003902531498407046), velocity: vector(0.01457401965494037, 0.008749957786090569["-u"](), 3.393201214360642e-7["-u"]()), color: color.green, name: "Earth" }),]);
    "97";
    mercury = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: earth.mass["*"](0.055)["/"](M), radius: 6000000["/"](AU), position: vector(0.360006238731298, 0.08310671431721671["-u"](), 0.03981766501010686["-u"]()), velocity: vector(0.0008732371820239134, 0.0286750815794258, 0.002263026727476856), color: color.red, name: "Mercury" }),]);
    "109";
    venus = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: earth.mass["*"](0.815)["/"](M), radius: 6000000["/"](AU), position: vector(0.5460148756311848["-u"](), 0.4654289630909307, 0.03789319798488837), velocity: vector(0.01319751648139675["-u"](), 0.01549708277964608["-u"](), 0.0005490020542624818), color: color.white, name: "Venus" }),]);
    "121";
    mars = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: earth.mass["*"](0.107)["/"](M), radius: 6000000["/"](AU), velocity: vector(0.01444719742599419, 0.0002365918534978303["-u"](), 0.000359488561244826["-u"]()), position: vector(0.1508529480814324["-u"](), 1.460121856503524["-u"](), 0.02689190873994556["-u"]()), color: color.red, name: "Mars" }),]);
    "131";
    jupiter = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: earth.mass["*"](318)["/"](M), radius: 70000000["/"](AU), velocity: vector(0.005611682808441865, 0.004596785105938998["-u"](), 0.0001064356940327842["-u"]()), position: vector(3.545075313382027["-u"](), 4.081361865858232["-u"](), 0.09627457319753692), color: color.blue, name: "Jupiter" }),]);
    "141";
    saturn = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: earth.mass["*"](95)["/"](M), radius: 60000000["/"](AU), velocity: vector(0.005262021976694793, 0.0004141890616120753, 0.0002169327374705523["-u"]()), position: vector(0.7842529344684837, 10.03393486265119["-u"](), 0.1431896871358062), color: color.white, name: "Saturn" }),]);
    "151";
    uranus = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: earth.mass["*"](14)["/"](M), radius: 25000000["/"](AU), velocity: vector(0.0019052013493924["-u"](), 0.003265505721711341, 0.000036690734434005), position: vector(17.46114323983198, 9.517430938519276, 0.1907513002050031["-u"]()), color: color.blue, name: "Uranus" }),]);
    "161";
    neptune = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: earth.mass["*"](17)["/"](M), radius: 24000000["/"](AU), velocity: vector(0.0008427417626787077, 0.003035037625808767, 0.00008199842541642128["-u"]()), position: vector(28.80079206580985, 8.17390036348871["-u"](), 0.495478418972816["-u"]()), color: color.yellow, name: "Neptune" }),]);
    "171";
    pluto = RS_interpolate_kwargs_constructor.call(Object.create(Body.prototype), false, Body, [RS_desugar_kwargs({ mass: earth.mass["*"](0.0022)["/"](M), radius: 1000000["/"](AU), position: vector(11.20198708794019, 31.64123744663468["-u"](), 0.1446313453325374), velocity: vector(0.003029567845289497, 0.0003743167934314588, 0.000926069693706297["-u"]()), color: color.yellow, name: "Pluto" }),]);
    "200"; return (function ___(__break) { var __more; var __loop = __cb(_, __frame, 0, 0, function __$main() { __more = false;
        var __1 = true; if (__1) {
          "201";
          return rate(10000, __cb(_, __frame, 219, 8, function __$main() {
            "202";
            RS_Iter4 = RS_Iterable(bodies);
            for (RS_Index4 = 0; RS_Index4["<"](RS_Iter4.length); RS_Index4++) {
              body = RS_Iter4[RS_Index4];
              "203";
              body.update(); };

            "204";
            time = time["+="](dt); while (__more) { __loop(); }; __more = true; }, true)); } else { __break(); } ; }); do { __loop(); } while (__more); __more = true; })(_); });};


if (!main.__argnames__) { Object.defineProperties(main, {
    __argnames__: { value: ["_",] } });};

;$(function(){ window.__context = { glowscript_container: $("#glowscript").removeAttr("id") }; main(__func) })})()
//--><!]]></script>
</div>
