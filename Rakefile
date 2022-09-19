# frozen_string_literal: true

# '2> /dev/null' silences only stderr
cmd_mutagen_check =
  'mutagen forward list ' \
  "#{ENV['HOSTNAME'][-11..]}-hugo " \
  '2> /dev/null'

cmd_hugo_mutagen_start =
  'mutagen forward create ' \
  "--name #{ENV['HOSTNAME'][-11..]}-hugo " \
  'tcp:localhost:1313 ' \
  'docker://$HOSTNAME:tcp:localhost:1313' \

cmd_hugo_mutagen_stop =
  'mutagen forward terminate ' \
  "#{ENV['HOSTNAME'][-11..]}-hugo"

# 1. check if there already is a mutagen forward session
# 2. else start a mutagen forward session
# 3. and start the hugo server and when it is killed
# 4. terminate the mutagen forward session
cmd_hugo_server =
  "#{cmd_mutagen_check} || (" \
  "#{cmd_hugo_mutagen_start} && " \
  "trap \"#{cmd_hugo_mutagen_stop}\" INT; " \
  'hugo server ' \
  '--source $(readlink -f /project/src/default); '\
  'trap - INT' \
  ')'
# hugo has problems reading git infos
# from a symlinked doc root as /project/src/default
# so to enable {{ .GitInfo | jsonify }}
# we have to get the symlink target with readlink -f

namespace :hugo do
  desc 'Run hugo server on localhost:1313'
  task :server do
    @commands << cmd_hugo_server
  end

  namespace :mutagen do |env|
    subtasks(env.scope.path) do
      desc 'Start mutagen forward of port 1313'
      task :start do
        @commands << cmd_hugo_mutagen_start
      end

      desc 'Stop mutagen forward of port 1313'
      task :stop do
        @commands << cmd_hugo_mutagen_stop
      end
    end
  end
end