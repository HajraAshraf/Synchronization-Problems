# Synchronization-Problems

reader-Writer Problem suing semaphores----
import java.util.concurrent.Semaphore;

public class ReaderWriterProblem {

    static Semaphore readSemaphore = new Semaphore(1);
    static Semaphore writeSemaphore = new Semaphore(1);
    static int readCount = 0;
    static int count = 0;

    static class Writer implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                try {
                    writeSemaphore.acquire();
                    
                    System.out.println("Writer entered");
                    
                    count++;
                    
                    System.out.println("Write content");
                    Thread.sleep(1000);
                    
                    writeSemaphore.release();
                    
                    System.out.println("Writer exit");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    static class Reader implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                try {
                    readSemaphore.acquire();
                    readCount++;
                    if (readCount == 1) {
                        writeSemaphore.acquire();
                    }
                    readSemaphore.release();

                    System.out.println("Reader entered");
                    Thread.sleep(1000);
                    System.out.println("Read content");

                    readSemaphore.acquire();
                    readCount--;
                    if (readCount == 0) {
                        writeSemaphore.release();
                    }
                    readSemaphore.release();
                    
                    System.out.println("Reader exit");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String[] args) {
        Thread[] threads = new Thread[5];

        for (int i = 0; i < 5; i++) {
            if (i <= 1) {
                threads[i] = new Thread(new Writer());
            } else {
                threads[i] = new Thread(new Reader());
            }
        }

        for (int i = 0; i < 5; i++) {
            threads[i].start();
        }

        for (int i = 0; i < 5; i++) {
            try {
                threads[i].join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
