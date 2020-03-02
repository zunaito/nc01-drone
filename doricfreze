-- freze (nc01-drone)
-- @glia
---------based largely on seekers by @tehn
E1 volume
-- E2 brightness
-- E3 density
-- K2 evolve
-- K3 worlds


sc = softcut -- typing shortcut
length = 0

world = 1

low = false

files = {"bc.wav","dd.wav","eb.wav"}

w_loop_start = {
  {28, 27.1, 34 },
  {5, 76, 33.3},
  {2, 1, 29} }

w_loop_end = {
  {26, 29, 30.42},
  {65, 74, 18.5},
  {3, 3, 37} }

w_rate = {
  {0.5, .25, -0.25},
  {1.4, .3, 1},
  {1, -1, 0.2} }

w_pan = {
  {0.25,-0.25,0},
  {0,0,0},
  {0.25, -0.25, 0} }

a = {}
v = {}

for i=1,15 do
  a[i] = math.random()*2*math.pi
  v[i] = math.random()*90 + 1
end



function load_file(w)
  file = _path.code .. "nc01-drone/lib/"..files[w]
  sc.buffer_read_mono(file,0,0,-1,1,1)
  _,length,sr = audio.file_info(file)
  length = length/sr
  print(audio.file_info(file))
end

function init_world(w)
  load_file(w)
  for i=1,3 do
    sc.rate(i,w_rate[w][i])
    sc.loop_start(i,w_loop_start[w][i])
    sc.loop_end(i,w_loop_end[w][i])
    sc.position(i,w_loop_start[w][i])
    sc.pan(i,w_pan[w][i])
  end
end

function set_density(x)
  sc.level(2,math.min(1,x*6))
  sc.level(3,math.max(0,x*2-1))
end

function set_brightness(x)
  for i=1,3 do
    sc.post_filter_lp(i,(1-x)*.6)
    sc.post_filter_hp(i,x*0.8)
  end
end


function init()
  cs_AMP = controlspec.new(0,1,'lin',0,2.0,'')
  params:add{type="control",id="volume",controlspec=cs_AMP,
    action=audio.level_dac}
  params:add{type="control",id="density",controlspec=cs_AMP,
    action=set_density}
  params:add{type="control",id="brightness",controlspec=cs_AMP,
    action=set_brightness}

  params:bang()

  for i=1,3 do
    sc.enable(i,1)
    sc.buffer(i,1)
    sc.level(i,1.0)
    sc.loop(i,1)
    sc.play(i,1)
    sc.rate_slew_time(i,0.1)
    sc.pan_slew_time(i,2)
    sc.post_filter_dry(i,0.2)
    sc.post_filter_fc(i,800)
    sc.post_filter_rq(i,10)
  end

  init_world(world)
end


function enc(n,d)
  if n==1 then
    params:delta("volume",d*4)
  elseif n==2 then
    params:delta("density",d*4)
  elseif n==3 then
    params:delta("brightness",d*4)
  end
end


function key(n,z)
  -- WORLD
  if n==3 and z==1 then
    world = (world%3)+1
    init_world(world)

    if world==2 then
      for i=1,15 do
        a[i] = math.random()*2*math.pi
        v[i] = math.random()*90 + 1
      end
    end
    redraw()

  -- EVOLVE
  elseif n==2 and z==1 then
    if world==1 then
      low = not low
      sc.rate(2,low and -1 or 1)
    elseif world==2 then
      for i=1,15 do
        a[i] = a[i] * (0.95 + math.random()*0.1)
        v[i] = v[i] * (0.95 + math.random()*0.1)
      end
      for i=1,3 do
        w_loop_start[2][i] = w_loop_start[2][i]+math.random()*2-1
        w_loop_end[2][i] = w_loop_end[2][i]+math.random()*2-1
        sc.loop_start(i,w_loop_start[2][i])
        sc.loop_end(i,w_loop_end[2][i])
      end

    elseif world==3 then
      low = not low
      sc.pan(1,low==false and 0.5 or -0.5)
      sc.pan(2,low==true and 0.5 or -0.5)
      sc.rate(3,low==false and 0.5 or 0.25)
      sc.rate_slew_time(3,low==false and 0.01 or 5)
    end
  redraw()

  end
end


function redraw()
  screen.clear()
  if world==1 then
    s = math.floor(math.random() * (txt:len()-2))
    screen.aa(0)
    screen.level(5)
    screen.move(2,3)
    screen.text_center(string.upper(txt:sub(s,s+5)))
    screen.move(2,4)
    screen.text_center(string.upper(txt:sub(s+6,s+5)))
    screen.move(2,8)
    screen.text_center(string.upper(txt:sub(s+1,s+5)))
  elseif world==2 then
    screen.aa(1)
    for i=1,15 do
      screen.move(30,30)
      screen.line_rel(math.cos(a[i])*v[i],math.sin(a[i])*v[i])
      screen.level(i)
      screen.stroke()
    end
  elseif world==3 then
    screen.aa(0)
    screen.rect(54,22,20,20)
    screen.level(2)
    screen.stroke()
  end

  screen.update()
end
