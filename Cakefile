# oriDomi Cakefile
# Dan Motzenbecker

fs            = require 'fs'
{exec, spawn} = require 'child_process'

tgthr = (fns, cb) ->
  res = {}
  out = (k) -> ->
    delete fns[k]
    res[k] = arguments
    cb res unless Object.keys(fns).length
  v out k for k, v of fns


announce = (name, fn) -> ->
  console.log "Running #{ name }..."
  fn.apply @, arguments


print = (cb) ->
  (err, stdout, stderr) ->
    throw err if err
    console.log stdout, stderr
    cb?.apply @, arguments


build = (cb) ->
  tgthr
    compile: (cb) -> compile null, -> minify null, cb
    docs:    (cb) -> docs    null, cb
    demo:    (cb) -> demo    null, cb
  , ->
    console.log 'Build succeeded.'
    cb?()


watch = ->
  coffee = spawn 'coffee', '-mwc oridomi.coffee'.split ' '
  coffee.stdout.pipe process.stdout
  coffee.stderr.pipe process.stderr


stats = ->
  tgthr
    coffee: (cb) -> fs.stat 'oridomi.coffee', cb
    js:     (cb) -> fs.stat 'oridomi.js',     cb
    mini:   (cb) -> fs.stat 'oridomi.min.js', cb
  , (stats) ->      console.log "#{ key }:\t#{ stat[1].size } B" for key, stat of stats


compile = announce 'compile', (args, cb) ->
  exec 'coffee -mc oridomi.coffee', print cb

minify  = announce 'minify',  (args, cb) ->
  exec 'uglifyjs --screw-ie8 -c unused=false -vmo oridomi.min.js oridomi.js', print cb

docs    = announce 'docs',    (args, cb) ->
  exec 'docco oridomi.coffee', print cb

demo    = announce 'demo',    (args, cb) ->
  exec 'stylus -u nib ./demo/demo.styl', print cb


task 'build',   'compile, minify, and generate annotated source', build
task 'watch',   'compile continuously',                           watch
task 'compile', 'compile .coffee to .js',                         compile
task 'minify',  'compress .js for production',                    minify
task 'docs',    'generate annotated source page in ./docs',       docs
task 'stats',   'output filesize stats',                          stats
task 'demo',    'compile demo site stylesheet',                   demo

