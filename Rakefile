#!/usr/bin/env ruby
require 'pry'

Drug = Struct.new(:name, :lab, :formula, :dose, :ve) do
  def to_json(*)
    to_h.to_json
  end
end

unless File.exist?('filea.pdf')
  `wget 'http://portal.anvisa.gov.br/wps/wcm/connect/95e50000436f3838ba7bfac9763a17cb/LISTA++A+DE+MED+REFER%C3%8ANCIA+17-03-2014.pdf?MOD=AJPERES' -O filea.pdf`
  `wget 'http://portal.anvisa.gov.br/wps/wcm/connect/16e5db00436f38bdba84fac9763a17cb/LISTA++B+DE+MED+REFER%C3%8ANCIA+17-03-2014.pdf?MOD=AJPERES' -O fileb.pdf`
end


desc 'Converts pdf to csv'
task :convert do
  puts 'Converting file a...'
  `jruby -S tabula filea.pdf --pages 1-28 --spreadsheet -o a.csv`

  puts 'Converting file b...'
  `jruby -S tabula fileb.pdf --pages 1-10 --spreadsheet -o b.csv`
end

DRUGS = []

desc 'Converts pdf to csv'
task :unify do
  require 'csv'
  require 'json'

  a = CSV.read('a.csv').to_a
  b = CSV.read('b.csv').to_a
  # name, lab: lab, formula: formula, dose: dose,
  a[6..-1].each do |c|
    start = c[2].empty? ? 3 : 2
    name, dose, ve = c[start..-1]
    DRUGS << Drug.new(name, c[1], c[0], dose, ve)
  end

  b[1..-1].each do |c|
    DRUGS << Drug.new(c[2], c[1], c[0], c[3], c[4])
  end

  File.open('drugs.json', 'w+') do |f|
    f << DRUGS.reject(&:nil?).flatten.to_json
  end

  JSON.parse(File.read('drugs.json'))
  binding.pry
end
