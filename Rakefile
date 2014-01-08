# ------------------------------------------------------------------------------
# Rake build script for oslic
# ------------------------------------------------------------------------------

require 'find'

VERSION_NUMBER = IO.read("rel-number.tex").chomp

PROTECTED_DIRS=%w{license}
AUX_EXTS=%w{url bbl blg aux dvi toc log lof nlo nls ilg ils ent}
RES_EXTS=%w{ps pdf rtf} + AUX_EXTS

DIR_REGEX=%r{^(\./)?#{PROTECTED_DIRS.join('|')}}
AUX_REGEX=%r{\.(#{AUX_EXTS.join('|')})$}
RES_REGEX=%r{\.(#{RES_EXTS.join('|')})$}

# ------------------------------------------------------------------------------
# Utilities for manipulating file names
# ------------------------------------------------------------------------------

def replace_ext(filename, new_ext) 
  filename.sub(/#{File.extname(filename)}$/, new_ext)
end

def no_ext(filename)
  replace_ext(filename, "")
end


# ------------------------------------------------------------------------------
# Commands used in this file
# ------------------------------------------------------------------------------

def latex(filename)
  sh "pdflatex #{no_ext(filename)}"
end

def make_bib_and_index(filename)
  index_input = replace_ext(filename, '.nlo')
  index_output = replace_ext(filename, '.nls')
  sh "bibtex #{no_ext(filename)}"
  sh "makeindex #{index_input} -s btexmat/nomencl.ist -o #{index_output}"
end

def fast_build(filename)
  basename = no_ext(filename)
  latex filename
  mv "#{basename}.pdf", "#{basename}-#{VERSION_NUMBER}.pdf"
end

# filename - name of the LaTeX file, w/ or w/o extension
#
def full_build(filename)
  latex filename
  make_bib_and_index filename
  latex filename
  fast_build filename
end

def remove_files
  Find.find(".") do |f|
    if yield(f) then
      rm f
    end
  end
end

# ------------------------------------------------------------------------------
# Tasks
# ------------------------------------------------------------------------------

task :clean do
  remove_files { |file| not(file =~ DIR_REGEX) && AUX_REGEX =~ file }
end

task :distclean do
  remove_files { |file| not(file=~ DIR_REGEX) && RES_REGEX =~ file }
end

task :build do 
  full_build "oslic"
end

task "oslic-#{VERSION_NUMBER}.pdf" do 
  fast_build "oslic"
end

# ------------------------------------------------------------------------------
# Local Variables:
# mode: ruby
# End:
# ------------------------------------------------------------------------------
